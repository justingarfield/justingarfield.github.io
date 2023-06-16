# Sidero Metal

This is part of a multi-part series:

* [Part 1 - Introduction](../README.md)
* [Part 2 - Hardware Preparation](hardware-preparation.md)
* [Part 3 - Software Preparation](software-preparation.md)
* [Part 4 - Sidero Metal](sidero-metal.md) <-- You are here
* [Part 5 - Talos OS]()
* [Part 6 - Provision a non Pi node]()

## Overview

In this part of the series, we focus on provisioning a single-node install of Sidero Metal and prepare it to provision other Talos OS machines on the network going forward.

Important to have readily available in other tabs/windows while installing and configuring...

* [Documentation -> Guides -> Sidero on Raspberry Pi 4](https://www.sidero.dev/v0.5/guides/sidero-on-rpi4/)
* [Documentation -> Reference -> Kernel](https://www.talos.dev/v1.3/reference/kernel/)
* [Documentation -> Reference -> Configuration](https://www.talos.dev/v1.3/reference/configuration)
* [https://github.com/siderolabs/cluster-api-bootstrap-provider-talos](https://github.com/siderolabs/cluster-api-bootstrap-provider-talos)

## High-level Overview of the Process

* Booting the Pi
  * Insert the SD Card you prepped earlier into the Pi you want to run Sidero on
  * Attach the Pi to the network your Kubernetes resources will reside on _(e.g. I use a separate VLAN and **/22** subnet that gives me ~1000 host IPs to work with)_
  * Boot the Pi and verify IP assigned and DNS is okay
* Get familiar with the config files
* Remotely provision Talos OS on the Pi
* Bootstrap single-node Kubernetes Control Plane
* Provision Sidero into single-node Kubernetes Control Plane
* Verify Sidero is running

## Step 1 - Booting the Pi

!! Important !!

* Make sure DHCP is configured and ready to go so that your Pi can grab an IP to start with
  * We'll assign it a Static in a few moments, this is just for initial provisioning
  * For my setup, I've reserved a DHCP block of 10 IPs for provisioning/debug purposes

Networking Side Notes:

* Talos will attempt to use `8.8.8.8` for DNS when booting if none is provided via DHCP. This can be changed by passing the [`ip` kernel parameter](https://www.talos.dev/v1.3/reference/kernel/#ip), but I wanted to use the OOTB Talos OS image and not have to worry about a custom one, so this didn't bother me. _(Note: I also re-write all DNS traffic at my firewall anyway, so that was another reason I didn't care about this initial call to use Google DNS, because it won't make it anyway)_

## Step 2 - Get familiar with the config files

First we're going to create a folder to work in, where we'll prepare the configuration files for Talos OS...exhilarating.

```bash
mkdir ~/sidero-provisioning
cd ~/sidero-provisioning
```

Now things get a little more meaty...We're going to generate 3 config files named `controlplane.yaml`, `talosconfig`, and `worker.yaml`. Creating them by hand would be miserable, so luckily we can use the `talosctl` binary to generate them for us based on OOTB templates and defaults.

Create a variable in your shell to hold the Pi's IP Address from Step 1. For example purposes, we'll assume the Pi received a DHCP address of `192.168.2.249`.

Next, use `talosctl` to create the configuration files (see below for example).

```bash
export SIDERO_ENDPOINT=192.168.2.249

talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/
```

Now, using something like VS Code, inspect the newly generated `controlplane.yaml`, `talosconfig`, and `worker.yaml` configuration files.

If we take a look at `talosconfig` first, we'll notice that it looks very similar to a `kubeconfig` file for `kubectl`...That's because eventually we'll use the values for `kubectl`.

Next in a browser, navigate to the following page [Documentation -> Reference -> Configuration](https://www.talos.dev/v1.3/reference/configuration) and skim through some of the materials at the top, mainly the [Config](https://www.talos.dev/v1.3/reference/configuration/#config) section.

Looking back at our config files, notice that the `controlplane.yaml` and `worker.yaml` files match the Talos OS Configuration reference we pulled up in the browser. Nice, now you now where the details for most of those settings are now.

If you scroll-down a little more in those two files, you'll find a commented-out section under `machine` -> `install` that looks like this:
```yaml
        # # Allows for supplying extra kernel args via the bootloader.
        # extraKernelArgs:
        #     - talos.platform=metal
        #     - reboot=k
```

If you want to configure `extraKernelArgs`, you can reference acceptable values here: [Documentation -> Reference -> Kernel](https://www.talos.dev/v1.3/reference/kernel/). Awesome, now you know how to pass kernel arguments to Talos OS if required for your environment.

## Provisioning Talos OS

Networking Side Notes:

* In my particular scenario, I'm provisioning my Kubernetes resources from another VLAN / subnet, so traffic traverses my firewall to route between networks. If you have a similar setup, you'll need to make sure that you allow `TCP/50000` for **apid** traffic and `TCP/6443` for **API Server** traffic. If you're provisioning from a machine that's on the same network VLAN / subnet as the other nodes, then you shouldn't have to worry about this.

Since not all environments are the same, you'll most likely need to change some of the defaults in the configurations that were generated by `talosctl`. For the purposes of this documentation, we absolutely need to change at the very least one of the defaults (master scheduling).

You can either edit the files directly; or use Configuration patch operations with `talosctl` to generate the modified configurations up-front. When creating patch operations, you'll need to reference the default configuration file(s) and adjust accordingly (e.g. `add` operations for sections that are commented-out / not in config, `replace` operations for sections that already have values)

For my setup I used the following:

* Allow Scheduling On Masters
  * Since we're building a Single-node Kubernetes Cluster to run Sidero Metal on-top of, we need to allow Sidero to be scheduled to this node, regardless of the fact that it's also the _master_ Control Plane node.
  * `{"op": "add", "path": "/cluster/allowSchedulingOnMasters", "value": true}`

* Change the disk to install Talos OS on
  * I have SATA-based SSDs attached via USB 3.0 ports on my Pis, so I want to target `/dev/sda` to install Talos on
  * If you want to install it on the SD Card you booted with, use the value of `/dev/mmcblk0` instead
  * `{"op": "replace", "path": "/machine/install/disk", "value": "/dev/sda"}`

* Assign a Static IP address
  * Our Sidero Metal server's `eth0` interface will get a static IP address of `192.168.2.5`
  * `{"op": "add", "path": "/machine/network/interfaces", "value": [ { "interface": "eth0", "addresses": [ "192.168.60.5/22" ] } ]}`

* Inform Talos that we're deploying a bare-metal platform, running on a Pi 4 board, and configure static IP, subnet mask, hostname, DNS server, and NTP server for kernel-level boot time (before Talos uses its config above)
  * `{"op": "add", "path": "/machine/install/extraKernelArgs", "value": [ "talos.platform=metal", "talos.board=rpi_4", "ip=192.168.60.5:::255.255.252.0:sidero-metal:eth0::192.168.60.1::192.168.60.1" ] }]'`

We can now put all of our configuration patch operations together and pass it to the same talosctl call like we did before. It should look similar to this:

```bash
talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/ --config-patch='[{"op": "add", "path": "/cluster/allowSchedulingOnMasters", "value": true}, {"op": "replace", "path": "/machine/install/disk", "value": "/dev/sda"}, {"op": "add", "path": "/machine/network/interfaces", "value": [ { "interface": "eth0", "addresses": [ "192.168.60.5/22" ] } ]}, {"op": "add", "path": "/machine/install/extraKernelArgs", "value": [ "talos.platform=metal", "talos.board=rpi_4", "ip=192.168.60.5::192.168.60.1:255.255.252.0:sidero-metal:eth0::192.168.60.1::192.168.60.1" ] }, {"op": "add", "path": "/machine/install/extensions", "value": [ { image: ghcr.io/justingarfield/sku_rm0004:0.1.4 } ]}]'

talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/ --config-patch='[{"op": "add", "path": "/cluster/allowSchedulingOnMasters", "value": true}, {"op": "replace", "path": "/machine/install/disk", "value": "/dev/sda"}, {"op": "add", "path": "/machine/network/interfaces", "value": [ { "interface": "eth0", "addresses": [ "192.168.60.5/22" ], "routes": [ { "network": "0.0.0.0/0", gateway: "192.168.60.1" } ] } ]}, {"op": "replace", "path": "/cluster/controlPlane/endpoint", "value": "https://192.168.60.5:6443/" }, {"op": "replace", "path": "/cluster/network/dnsDomain", "value": "sidero-metal.kubernetes.lesmerises.jgarfield.com" }, {"op": "add", "path": "/machine/install/extraKernelArgs", "value": [ "talos.platform=metal", "talos.board=rpi_4", "ip=192.168.60.5::192.168.60.1:255.255.252.0:sidero-metal:eth0::192.168.60.1::192.168.60.1" ] }, {"op": "add", "path": "/machine/install/extensions", "value": [ { image: ghcr.io/justingarfield/sku_rm0004:0.1.4 } ]}]'
```

```bash
talosctl apply-config --insecure -n ${SIDERO_ENDPOINT} -f controlplane.yaml

export SIDERO_STATIC_ENDPOINT=192.168.60.5
talosctl config merge talosconfig
talosctl config endpoints ${SIDERO_STATIC_ENDPOINT}
talosctl config nodes ${SIDERO_STATIC_ENDPOINT}

talosctl version

talosctl bootstrap

talosctl kubeconfig

talosctl dmesg -f

kubectl get nodes
```

Place in: `~/.cluster-api/clusterctl.yaml`:
```
providers:
  - name: "talos"
    url: "https://github.com/siderolabs/cluster-api-bootstrap-provider-talos/releases/latest/bootstrap-components.yaml"
    type: "BootstrapProvider"
  - name: "talos"
    url: "https://github.com/siderolabs/cluster-api-control-plane-provider-talos/releases/latest/control-plane-components.yaml"
    type: "ControlPlaneProvider"
  - name: "sidero"
    url: "https://github.com/siderolabs/sidero/releases/latest/infrastructure-components.yaml"
    type: "InfrastructureProvider"
```

```bash
SIDERO_CONTROLLER_MANAGER_HOST_NETWORK=true SIDERO_CONTROLLER_MANAGER_API_ENDPOINT=${SIDERO_STATIC_ENDPOINT} clusterctl init -i sidero -b talos -c talos

watch -n 2 kubectl get pods -A

curl -I http://${SIDERO_STATIC_ENDPOINT}:8081/tftp/ipxe.efi
```

## Changes from guide

* Renamed cluster name from `rpi4-sidero` to `sidero-metal`
* `{"op": "replace", "path": "/machine/install/disk", "value": "/dev/sda"}`
* `{"op": "add", "path": "/machine/network", "value": { interfaces: [ { interface: eth0, addresses: [ \"192.168.60.5\" ] } ]}}`
* `{"op": "add", "path": "/machine/install/extraKernelArgs", "value": [ "talos.platform=metal", "talos.board=rpi_4", "ip=192.168.60.5::192.168.60.1:255.255.252.0:sidero-metal:eth0::192.168.60.1::192.168.60.1" ] }`
* `{"op": "add", "path": "/machine/install/extensions", "value": [ { image: ghcr.io/justingarfield/SKU_RM0004 } ] }`
* `{"op": "replace", "path": "/cluster/controlPlane/endpoint", "value": "https://192.168.60.5:6443/" }`
* `{"op": "replace", "path": "/cluster/network/dnsDomain", "value": "sidero-metal.kubernetes.lesmerises.jgarfield.com" }`
* `{"op": "replace", "path": "/cluster/apiServer/certSANs", "value": ["192.168.60.5", "sidero-metal.kubernetes.lesmerises.jgarfield.com"] }`

## Other

* For machines with multiple NICs, see: [talos.network.interface.ignore](https://www.talos.dev/v1.3/reference/kernel/#talosnetworkinterfaceignore)
* If for some odd reason you have older workloads on cgroups v1, see: [talos.unified_cgroup_hierarchy](https://www.talos.dev/v1.3/reference/kernel/#talosunified_cgroup_hierarchy)


```bash
talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/ --config-patch='[{"op": "add", "path": "/cluster/allowSchedulingOnMasters", "value": true},{"op": "add", "path": "/machine/install/extensions", "value": [ { image: ghcr.io/justingarfield/sku_rm0004:0.1.10 } ]}]'

talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/ --config-patch='[{"op": "add", "path": "/cluster/allowSchedulingOnMasters", "value": true},{"op": "add", "path": "/machine/install/extensions", "value": [ { image: ghcr.io/justingarfield/hello-world-service:0.1.1 } ]}]'

talosctl apply-config --insecure -n ${SIDERO_ENDPOINT} -f controlplane.yaml

talosctl config merge talosconfig
talosctl config endpoints ${SIDERO_ENDPOINT}
talosctl config nodes ${SIDERO_ENDPOINT}

talosctl version

talosctl -n ${SIDERO_ENDPOINT} upgrade --preserve --image=ghcr.io/siderolabs/installer:v1.4.0-alpha.2-5-gdea17d723

talosctl reset --graceful=false --reboot=true --nodes ${SIDERO_STATIC_ENDPOINT}
```
