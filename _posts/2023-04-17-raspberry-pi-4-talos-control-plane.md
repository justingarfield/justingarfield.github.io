---
layout: post
title: Talos v1.4 Control Plane w/ Cilium on Raspberry Pi 4B
tags: talos cilium kubernetes
---

* Overview
* Creating the Configuration Files

## Overview

This post dives deeper into setting up a 3-node Control Plane using the [Talos Linux Getting Started](https://www.talos.dev/v1.4/introduction/getting-started/) guide. It targets using the Raspberry Pi 4B as the control plane nodes.

This post assumes you've already prepped your node(s) with a fresh install of Talos Linux.

## <a name="#creating-config-files">Creating the configuration files</a> 

{% capture warning_note %}
<p>Every file created in this post will contain sensitive information!</p>
<p>The post itself supplies throw-away dummy values that don't matter.</p>
<p><strong>Make sure you store your own files in a secure location when finished setting up your cluster(s), as they hold keys, certificates, network information, etc.</strong></p>
{% endcapture %}
{% include warning-bubble.html content=warning_note %}

Generating and tweaking the Talos Machine Configuration files will probably be where most of your time is spent. It took me a lot of trial-and-error with different configuration values to get everything working the way I wanted (DNS, Hostnames, etc.)

You don't HAVE to follow my pattern(s) for dealing with Machine Configurations and Patch Files. You can just have a single machine config that you edit directly before/after each node you deploy; it really depends on your environment, cluster node types, hardware being used, etc. This just happens to be what worked well for me with my at-home cluster.

Let's have a look at the configuration files that are about to get created in this post...

![Talos Linux Machine Configuration Overview](/img/talos-machine-configurations.drawio.png)

A few things to make note of:

* The Cilium Manifest should only get deployed to the Control Plane nodes, _never_ on Worker Plane nodes
* The Secrets Bundle is used for both Control Plane _and_ Worker Plane nodes
* Patch files are used to transform/modify a Machine Configuration, so that they can easily be tweaked for different environments
* I show the Worker Plane Patch file in the diagram, but I personally didn't have to use it since the All Nodes patch was enough for my environment

### Folder Layout

```bash
my-talos-cluster\
    machine-configs\
        node-cp01.yaml
        node-cp02.yaml
        node-cp03.yaml
        node-wk01.yaml
        node-wk02.yaml
    machine-templates\
        controlplane.yaml
        worker.yaml
    patches\
        all-nodes.patch
        controlplane.patch
        node-cp01.patch
        node-cp02.patch
        node-cp03.patch
        node-wk01.patch
        node-wk02.patch
        worker.patch
    secrets-bundle.yaml
    talosconfig
```

### Generate a Secrets Bundle

Working our way from left-to-right in the diagram above, we'll start with the Secrets Bundle since it's required for both planes.

```bash
talosctl gen secrets -o template/secrets-bundle.yaml
```

If you happen to have your own PKI or Cluster Bootstrap Token, you can also use the `--from-kubernetes-pki` and `--kubernetes-bootstrap-token` options respectively.

### Generate the Cilium K8s Manifest

There are [multiple ways to install Cilium on Talos](https://www.talos.dev/v1.4/kubernetes-guides/network/deploying-cilium). This post will focus on using [Method 4: Helm manifests inline install](https://www.talos.dev/v1.4/kubernetes-guides/network/deploying-cilium/#method-4-helm-manifests-inline-install) with `kube-proxy` disabled.

```bash
helm repo add cilium https://helm.cilium.io/
helm repo update

export KUBERNETES_API_SERVER_ADDRESS=cp.k8s.mydomain.tld
export KUBERNETES_API_SERVER_PORT=6443
export CILIUM_VERSION=1.13.0

helm template cilium cilium/cilium \
    --version ${CILIUM_VERSION} \
    --namespace kube-system \
    --set ipam.mode=kubernetes \
    --set=kubeProxyReplacement=strict \
    --set=securityContext.capabilities.ciliumAgent="{CHOWN,KILL,NET_ADMIN,NET_RAW,IPC_LOCK,SYS_ADMIN,SYS_RESOURCE,DAC_OVERRIDE,FOWNER,SETGID,SETUID}" \
    --set=securityContext.capabilities.cleanCiliumState="{NET_ADMIN,SYS_ADMIN,SYS_RESOURCE}" \
    --set=cgroup.autoMount.enabled=false \
    --set=cgroup.hostRoot=/sys/fs/cgroup \
    --set=k8sServiceHost="${KUBERNETES_API_SERVER_ADDRESS}" \
    --set=k8sServicePort="${KUBERNETES_API_SERVER_PORT}" > cilium-helm-k8s-manifest.yaml
```

Assuming that you're using a Load Balancer in your setup, you'll want to set the `KUBERNETES_API_SERVER_ADDRESS` variable to a DNS name that points to the Load Balancer IP Addaress; if a DNS record isn't available, set this to the Load Balancer's IP Address.

Let's breakdown what's going on here...

| Parameter | Description |
| :--- | :--- |
| `--version` | the version of cilium to install |
| `--namespace` | the k8s namespace to place the cilium components in |
| [`--set ipam.mode=kubernetes`](https://docs.cilium.io/en/stable/network/concepts/ipam/kubernetes/) | what cilium should use for IP Address Management (`kubernetes` = `kube-controller-manager` pod CIDR allocation) |
| [`--set=kubeProxyReplacement=strict`](https://docs.cilium.io/en/stable/network/kubernetes/kubeproxy-free/#kube-proxy-hybrid-modes) | how the cilium `kube-proxy` replacement should behave |
| [`--set=securityContext.capabilities.*`](https://github.com/cilium/cilium/issues/21603) | specifies the required capabilities needed for the Cilium Components |
| [--set=cgroup.autoMount.enabled=false](#) |  |
| [--set=cgroup.autoMount.enabled=/sys/fs/cgroup](#) |  |
| [--set=k8sServiceHost](#) | |
| [--set=k8sServiceHost](#) | |
| [Cilium Helm Reference](https://docs.cilium.io/en/stable/helm-reference) | more detailed explanation of available options and what they do can be found here |

### Generate the All Nodes Patch

Create a file named `patches/all-nodes.patch`...

```yaml
version: v1alpha1
persist: true
machine:
  certSANs:
    - talos-endpoints.k8s.mydomain.tld
    - talos-endpoints
    - 192.168.22.8
  install:
    disk: /dev/sda
    image: ghcr.io/siderolabs/installer:v1.4.0
  network:
    interfaces:
      - interface: eth0
        dhcp: false
        routes:
          - network: 0.0.0.0/0
            gateway: 192.168.22.1
    nameservers:
      - 192.168.22.1
  time:
    disabled: false
    servers:
      - 192.168.22.1
    bootTimeout: 2m0s
  kubelet:
    image: ghcr.io/siderolabs/kubelet:v1.27.1
    clusterDNS:
      - 10.96.0.10
```

Let's examine some of the sections in the configuration YAML file above, as these are changing the default behavior of Talos.

#### Machine: Images

```yaml
image: ghcr.io/siderolabs/installer:v1.4.0
image: ghcr.io/siderolabs/kubelet:v1.27.1
```

Notice that the image declarations are using fixed version numbers. This is important if you want to be able to re-create the same environment down the road, let's say in the event of a full hardware failure (voltage spike fries your Pis or something).

If you don't use fixed version numbers, you may encounter a completely different experience and set of behaviors re-installing your cluster even on matching-hardware. I'm sure somewhere along all the images / helm charts I use, there's probably something pulling `latest`, but this at least gives me a fighting chance at having sanity after a disaster / major outage.

Note: If you're wondering why `kube-proxy` isn't defined anywhere, it's because it never gets installed. I chose to go the route of using Cilium's `kube-proxy` replacement, so instead you'll see a `proxy: disabled: true` setting get applied further on in the article (down in the Control Plane Patch).

#### Machine: CertSANS

```yaml
certSANs:
  - talos-endpoints.k8s.mydomain.tld
  - talos-endpoints
  - 192.168.22.8
```

Since I use a load balancer in-front of my Talos Endpoints, I won't be hitting them directly when utilizing `talosctl` to manage clusters / nodes.

In order for `talosctl` to not throw an invalid certificate error when establishing a connection to the endpoints, we need to ensure that a matching Subject Alternative Name (SAN) exists for the DNS name (and/or load balancer IP + DNS Hostname w/o Domain)

#### Machine: Install

```yaml
install:
    disk: /dev/sda
```

Since this particular setup is using USB-SSD storage, our target install disk should be `/dev/sda` vs. `/dev/mmcblk0` for SD Cards.

This directive will only affect future Talos Upgrades, not the current install, as a Raspberry Pi image is considered a "raw disk image", which has essentially already been installed onto the SSD.

#### Machine: Network

```yaml
network:
  interfaces:
    - interface: eth0
      dhcp: false
      routes:
        - network: 0.0.0.0/0
          gateway: 192.168.22.1
  nameservers:
    - 192.168.22.1
```

I chose to statically assign my network information, even though I also have DHCP configure it before fully provisioning Talos. This allows me to still get Talos Nodes online in the event my DHCP service is down for whatever reason.

I also like to statically define the nameservers, as Talos will attempt to use Google's 8.8.8.8 by default (which in my environment just gets forwarded to a local DNS server anyway).

You'll notice that node-specifics aren't included here, as this is a shared Machine Config used to create the Node-specific ones later. We'll store node-specific items in their own files.

#### Machine: Time

```yaml
time:
  disabled: false
  servers:
    - 192.168.22.1
  bootTimeout: 2m0s
```

This is just ensuring that Talos points to my own NTP server vs. attempting to reach out to the internet for one.

While not usually a problem, you definitely want to make sure that NTP is synchronizing to the same source (and syncing reguarly), because if your cluster nodes get too much time drift, your cluster will practically explode since no one will trust each other (due to expiration windows)

#### Machine: Kubelet

```yaml
kubelet:
  clusterDNS:
    - 10.96.0.10
```

In my particular environment, I need to specifically tell kubelet to provide a DNS server of `10.96.0.10` to all pods in the cluster.

Otherwise they get fed the Talos Nodes' `/etc/resolv.conf` file, which only contains my private / external DNS server, so pods can't resolve in-cluster resources properly.

### Generate the Control Plane Patch

Create a file named `patches/controlplane.patch`...

```yaml
cluster:
  apiServer:
    image: registry.k8s.io/kube-apiserver:v1.27.1
  controllerManager:
    image: registry.k8s.io/kube-controller-manager:v1.27.1
  discovery:
    registries:
      kubernetes:
        disabled: false
      service:
        disabled: true
  network:
    dnsDomain: k8s.mydomain.tld
    cni:
      name: none
  proxy:
    disabled: true
  scheduler:
    image: registry.k8s.io/kube-scheduler:v1.27.1
  inlineManifests:
    - name: cilium
      contents: |
        - place-holder
```

#### Cluster: Images

```yaml
image: registry.k8s.io/kube-apiserver:v1.27.1
image: registry.k8s.io/kube-controller-manager:v1.27.1
image: registry.k8s.io/kube-scheduler:v1.27.1
```

Just locking in versions of container images we plan on using for our cluster. Same as above in the All Nodes Patch section.

#### Cluster: Discovery

```yaml
discovery:
  registries:
    kubernetes:
      disabled: false
    service:
      disabled: true
```

Talos will attempt to use a [cloud-based discovery service that SideroLabs hosts](https://www.talos.dev/v1.4/talos-guides/discovery/) themselves, which aids in forming clusters in environments that would otherwise require very tricky configurations. This service _is_ safe to use, as it encrypts all traffic to/from their discovery service, as well as removing your discovery information from their system in a relatively short timespan.

In my particular environment, I decided to disable the cloud-based discovery, and go back to having discovery happen through K8s / etcd. Since I have full control over my network environment, and can just walk downstairs and physically work on my equipment, I didn't feel the need for more network traffic exiting my home network, just to find a node sitting 1-inch from the others in the same VLAN.

#### Cluster: Network

```yaml
network:
    dnsDomain: k8s.mydomain.tld
    cni:
      name: none
```

If you use anything other than the default `cluster.local` DNS Domain for your internal K8s DNS, specify that here using `dnsDomain`.

Since this configuration is also going to use Cilium for a CNI _(instead of Flannel which is the default)_, we set `cni: name: none`. This instructs Talos to not provision a CNI as part of its bootstrap process. We'll instead install Cilium ourselves using an Inline Manifest.

#### Cluster: Proxy

```yaml
proxy:
  disabled: true
```

This ensures that `kube-proxy` doesn't get installed on the Talos Nodes. We don't want it getting installed since this post goes the route of using the Cilium `kube-proxy` replacement.

#### Cluster: Inline Manifests

```yaml
inlineManifests:
  - name: cilium
    contents: |
      - place-holder
```

This is a place-holder / stub that allows us to use `yq` later to inject the Cilium K8s Manifest into our Control Plane Machine Config.

### Add Cilium K8s Manifest to Control Plane Patch

Next we add Cilium support with an "Inline Manifest"...
https://www.talos.dev/v1.3/kubernetes-guides/network/deploying-cilium/#method-4-helm-manifests-inline-install

```bash
yq -i '.cluster.inlineManifests[0].contents |= load_str("cilium-helm-k8s-manifest.yaml")' ./machine-configs/control-plane.yaml
```

This will find the `place-holder` property you added to the Control Plane Patch file in the previous step, and replace the place-holder content with the actual Cilium K8s Manifest located in `cilium-helm-k8s-manifest.yaml`

After you're satisfied that you have the Cilium K8s Manifest correctly added to the Control Plane Patch, you can feel free to delete it, since you've captured all the contents in the Patch now. _I kept mine around while I worked through building this all out, but now I auto-delete it as part of a script)_

### Generate the Plane Machine Configurations

```bash
talosctl gen config hybrid-cluster https://hybrid-control-plane.kubernetes.lesmerises.jgarfield.com:6443 \
    --with-secrets secrets-bundle.yaml \
    --additional-sans=hybrid-control-plane,192.168.22.8 \
    --config-patch='[{"op": "remove", "path": "/machine/certSANs"}]' \
    --config-patch @./talos-linux/patch-manual-controlplane-config.yaml \
    --with-docs=false \
    --with-examples=false \
    --output ./_out
```

### Create Node Patches



### Generate the final Node Machine Configs

```bash
# Do something a little more fancy eventually
# ls ./patches/node-*.patch | while read patchFile; do talosctl machineconfig patch ./patches/controlplane.yaml --patch @$$patchFile --output ./machine-configs/$$patchFile.yaml
talosctl machineconfig patch ./machine-templates/controlplane.yaml --patch @./patches/node-cp01.patch --output ./machine-configs/node-cp01.yaml
talosctl machineconfig patch ./machine-templates/controlplane.yaml --patch @./patches/node-cp02.patch --output ./machine-configs/node-cp02.yaml
talosctl machineconfig patch ./machine-templates/controlplane.yaml --patch @./patches/node-cp03.patch --output ./machine-configs/node-cp03.yaml
talosctl machineconfig patch ./machine-templates/worker.yaml --patch @./patches/node-wk01.patch --output ./machine-configs/node-wk01.yaml
talosctl machineconfig patch ./machine-templates/worker.yaml --patch @./patches/node-wk02.patch --output ./machine-configs/node-wk02.yaml
```

### Configure $HOME/.talos/config

```bash
talosctl config merge ./talosconfig
talosctl config endpoints talos-cluster-endpoints.k8s.mydomain.tld
```

### Apply Machine Configs to Nodes

```bash
talosctl apply-config --insecure --nodes cp01.k8s.mydomain.tld --file ./machine-configs/node-cp01.yaml
talosctl apply-config --insecure --nodes cp02.k8s.mydomain.tld --file ./machine-configs/node-cp02.yaml
talosctl apply-config --insecure --nodes cp03.k8s.mydomain.tld --file ./machine-configs/node-cp03.yaml
talosctl apply-config --insecure --nodes wk01.k8s.mydomain.tld --file ./machine-configs/node-wk01.yaml
talosctl apply-config --insecure --nodes wk02.k8s.mydomain.tld --file ./machine-configs/node-wk02.yaml
```

### Light your cluster up!

```bash
talosctl bootstrap --nodes hybrid-cp01.kubernetes.lesmerises.jgarfield.com
```

```
talosctl kubeconfig --nodes hybrid-cp01.kubernetes.lesmerises.jgarfield.com
```

### Ruh roh!

This is usually the point where things can start to go wrong. Be it misconfigured DNS, bad configuration settings, etc.

Be prepared to heavily use commands like:
`talosctl` and `kubectl`

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

{% capture security_note %}
<p>Every file you just created contains sensitive information!</p>
<p>Now is the time to make backups and/or move these files off your machine to a more secure location.</p>
{% endcapture %}
{% include security-bubble.html content=security_note %}

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
