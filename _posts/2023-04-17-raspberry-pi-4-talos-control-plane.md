---
title: Raspberry Pi 4 Talos v1.4 Control Plane w/ Cilium
---

## Overview

This post dives deeper into setting up a 3-node Control Plane using the [Talos Linux Getting Started](https://www.talos.dev/v1.4/introduction/getting-started/) guide. It targets using the Raspberry Pi 4B as the control plane nodes.

A [seperate post](#) covers setting up the worker node(s).

This post assumes you've already prepped your node(s) with a fresh install of Talos Linux.

{% capture warning_note %}
Every file created in this post will contain sensitive information!
<br />
The post itself supplies throw-away dummy values that don't matter.
<br />
Make sure you store your own files in a secure location when finished setting up your cluster(s), as they hold keys, certificates, network information, etc.
{% endcapture %}
{% include warning-bubble.html content=warning_note %}

## Creating the configuration files

Generating and tweaking the Talos Machine Configuration files will probably be where most of your time is spent. It took me a lot of trial-and-error with different configuration values to get everything working the way I wanted (DNS, Hostnames, etc.)

You don't HAVE to follow my pattern(s) for dealing with Machine Configurations and Patch Files. You can just have a single machine config that you edit directly before/after each node you deploy; it really depends on your environment, cluster node types, hardware being used, etc. This just happens to be what worked well for me with my at-home cluster.

Let's have a look at the configuration files that are about to get created in this post...

![Talos Linux Machine Configuration Overview](/img/talos-machine-configurations.drawio.png)

A few things to make note of:

* The Cilium Manifest should only get deployed to the Control Plane nodes, _never_ on Worker Plane nodes
* The Secrets Bundle is used for both Control Plane _and_ Worker Plane nodes
* Patch files are used to transform/modify a Machine Configuration, so that they can easily be tweaked for different environments

### Folder Layout

```bash
my-talos-cluster\
    template\
        control-plane.patch
        cp01.patch
        cp02.patch
        cp03.patch
        wk01.patch
        wk02.patch
        secrets-bundle.yaml
        worker-plane.patch
    machine-configs\
        cp01.yaml
        cp02.yaml
        cp03.yaml
        wk01.yaml
        wk02.yaml
```

### Generate a Secrets Bundle

Working our way from left-to-right in the diagram above, we'll start with the Secrets Bundle since it's required for both planes.

```bash
talosctl gen secrets -o template/secrets-bundle.yaml
```

If you happen to have your own PKI or Cluster Bootstrap Token, you can use the `--from-kubernetes-pki` and `--kubernetes-bootstrap-token` options respectively.

### Generate the Cilium K8s Manifest

There are [multiple ways to install Cilium on Talos](https://www.talos.dev/v1.4/kubernetes-guides/network/deploying-cilium). This post will focus on using [Method 4: Helm manifests inline install](https://www.talos.dev/v1.4/kubernetes-guides/network/deploying-cilium/#method-4-helm-manifests-inline-install) with `kube-proxy` disabled.


```bash
helm repo add cilium https://helm.cilium.io/
helm repo update

export KUBERNETES_API_SERVER_ADDRESS=hybrid-control-plane.kubernetes.lesmerises.jgarfield.com
export KUBERNETES_API_SERVER_PORT=6443

helm template cilium cilium/cilium \
    --version 1.13.0 \
    --namespace kube-system \
    --set ipam.mode=kubernetes \
    --set=kubeProxyReplacement=strict \
    --set=securityContext.capabilities.ciliumAgent="{CHOWN,KILL,NET_ADMIN,NET_RAW,IPC_LOCK,SYS_ADMIN,SYS_RESOURCE,DAC_OVERRIDE,FOWNER,SETGID,SETUID}" \
    --set=securityContext.capabilities.cleanCiliumState="{NET_ADMIN,SYS_ADMIN,SYS_RESOURCE}" \
    --set=cgroup.autoMount.enabled=false \
    --set=cgroup.hostRoot=/sys/fs/cgroup \
    --set=k8sServiceHost="${KUBERNETES_API_SERVER_ADDRESS}" \
    --set=k8sServicePort="${KUBERNETES_API_SERVER_PORT}" > cilium.yaml
```

### Generate the Control Plane Patch

Create a file named `patch-manual-controlplane-config.yaml`

`<insert example file for reference>`

Next we add Cilium support with an "Inline Manifest"...
https://www.talos.dev/v1.3/kubernetes-guides/network/deploying-cilium/#method-4-helm-manifests-inline-install

Now add inline manifest to control-plane config file...

```
TODO: use yq to automate this part

cluster:
  inlineManifests:
    - name: cilium
      contents: |
        <insert cilium.yaml file contents here>
```

```bash
talosctl gen config hybrid-cluster https://hybrid-control-plane.kubernetes.lesmerises.jgarfield.com:6443 \
    --with-secrets secrets-bundle.yaml \
    --additional-sans=hybrid-control-plane,192.168.68.8 \
    --config-patch='[{"op": "remove", "path": "/machine/certSANs"}]' \
    --config-patch @./talos-linux/patch-manual-controlplane-config.yaml \
    --with-docs=false \
    --with-examples=false \
    --output ./_out
```

```bash
talosctl machineconfig patch _out/controlplane.yaml --patch @./talos-linux/hybrid-cluster/hybrid-cp01.patch --output _out/hybrid-cp01.yaml
talosctl machineconfig patch _out/controlplane.yaml --patch @./talos-linux/hybrid-cluster/hybrid-cp02.patch --output _out/hybrid-cp02.yaml
talosctl machineconfig patch _out/controlplane.yaml --patch @./talos-linux/hybrid-cluster/hybrid-cp03.patch --output _out/hybrid-cp03.yaml
talosctl machineconfig patch _out/worker.yaml --patch @./talos-linux/hybrid-cluster/wk01.patch --output _out/wk01.yaml

talosctl config merge ./_out/talosconfig
talosctl config endpoints hybrid-talos-endpoints.kubernetes.lesmerises.jgarfield.com

talosctl apply-config --insecure --nodes hybrid-cp01.kubernetes.lesmerises.jgarfield.com --file _out/cp01.yaml
talosctl apply-config --insecure --nodes hybrid-cp02.kubernetes.lesmerises.jgarfield.com --file _out/cp02.yaml
talosctl apply-config --insecure --nodes hybrid-cp03.kubernetes.lesmerises.jgarfield.com --file _out/cp03.yaml
talosctl apply-config --insecure --nodes hybrid-wk01.kubernetes.lesmerises.jgarfield.com --file _out/wk01.yaml
talosctl apply-config --insecure --nodes hybrid-wk02.kubernetes.lesmerises.jgarfield.com --file _out/wk02.yaml

talosctl bootstrap --nodes hybrid-cp01.kubernetes.lesmerises.jgarfield.com
```

This is usually the point where things can start to go wrong. Be it misconfigured DNS, bad configuration settings, etc.

Be prepared to heavily use commands like:
`talosctl` and `kubectl`

```
talosctl kubeconfig --nodes hybrid-cp01.kubernetes.lesmerises.jgarfield.com
```

If your node(s) don't seem to be fully booting and keep showing NotReady state, check the logs of the Cilium deployment...

```bash
kubectl -n kube-system logs deployment/cilium-operator

kubectl label --overwrite ns cilium-test pod-security.kubernetes.io/enforce=privileged
cilium connectivity test
```

```bash
kubectl delete -n cilium-test deployment echo-same-node
kubectl delete -n cilium-test service echo-same-node
kubectl delete -n cilium-test serviceaccount client client2 default echo-same-node


kubectl taint node hybrid-cp01 node-role.kubernetes.io/control-plane:NoSchedule-
kubectl debug -n kube-system pod/coredns-5597575654-t56mc -it --image=nicolaka/netshoot
kubectl debug -n kube-system pod/kube-apiserver-hybrid-cp01 -it --image=nicolaka/netshoot
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot
```

## References

### Docs

* [kube-apiserver](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)
* [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
* [Method 4: Helm manifests inline install](https://www.talos.dev/v1.4/kubernetes-guides/network/deploying-cilium/#method-4-helm-manifests-inline-install)
* [Accessing the Kubernetes API from a Pod](https://kubernetes.io/docs/tasks/run-application/access-api-from-pod/)
* [Service Map & Hubble UI](https://docs.cilium.io/en/stable/gettingstarted/hubble/)
* [Anatomy of the "kubernetes.default"](https://networkop.co.uk/post/2020-06-kubernetes-default/)
* [Kubelet Configuration (v1beta1)](https://kubernetes.io/docs/reference/config-api/kubelet-config.v1beta1/)
* [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
* [Listing all resources in a namespace](https://stackoverflow.com/questions/47691479/listing-all-resources-in-a-namespace)
* [Apply Pod Security Standards at the Cluster Level](https://kubernetes.io/docs/tutorials/security/cluster-level-pss/)
* [Cilium: Ops: Troubleshooting](https://docs.cilium.io/en/stable/operations/troubleshooting/)

### Source

* [coreDNS K8s Plugin](https://github.com/coredns/coredns/tree/master/plugin/kubernetes)

### Tools

* [netshoot](https://github.com/nicolaka/netshoot)

### Troubleshooting

* [Kube-proxy replacement not working when running without privileges](https://github.com/cilium/cilium/issues/21603)
* [Getting the dial tcp 10.96.0.1:443: i/o timeout issues](https://discuss.projectcalico.tigera.io/t/getting-the-dial-tcp-10-96-0-1-i-o-timeout-issues/38/3)
* [Kubernetes pod /etc/resolv.conf has the wrong nameserver](https://serverfault.com/questions/1009907/kubernetes-pod-etc-resolv-conf-has-the-wrong-nameserver)
* [Connectivity test failing at lookup for kubernetes.default after fresh install](https://github.com/cilium/cilium/issues/22997)
