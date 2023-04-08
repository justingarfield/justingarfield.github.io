# Sidero

* TODO: Add SHA checks to binary downloads

## Pre-reqs on WSL

Here we'll download and install all of the required administrative CLI binaries to build-out our Sidero cluster(s).

```bash
#--- Create a downloads folder in your WSL /home/<username> Directory
mkdir -p $HOME/downloads

export TOOLING_ARCH="amd64"
export TOOLING_OS=$(uname -s | tr "[:upper:]" "[:lower:]")

#--- Install `clusterctl`
export CLUSTERCTL_VERSION="v1.3.5"
export CLUSTERCTL_FILENAME="clusterctl-${TOOLING_OS}-${TOOLING_ARCH}"
export CLUSTERCTL_URL="https://github.com/kubernetes-sigs/cluster-api/releases/download/${CLUSTERCTL_VERSION}/${CLUSTERCTL_FILENAME}"

curl --location --output $HOME/downloads/${CLUSTERCTL_FILENAME}-${CLUSTERCTL_VERSION} ${CLUSTERCTL_URL}
sudo install -o root -g root -m 0755 $HOME/downloads/${CLUSTERCTL_FILENAME}-${CLUSTERCTL_VERSION} /usr/local/bin/${CLUSTERCTL_FILENAME}-${CLUSTERCTL_VERSION}
sudo rm /usr/local/bin/clusterctl
sudo ln /usr/local/bin/${CLUSTERCTL_FILENAME}-${CLUSTERCTL_VERSION} /usr/local/bin/clusterctl
rm $HOME/downloads/${CLUSTERCTL_FILENAME}-${CLUSTERCTL_VERSION}

#--- Install 'talosctl`
export TALOSCTL_VERSION="v1.3.6"
export TALOSCTL_FILENAME="talosctl-${TOOLING_OS}-${TOOLING_ARCH}"
export TALOSCTL_URL="https://github.com/siderolabs/talos/releases/download/${TALOSCTL_VERSION}/${TALOSCTL_FILENAME}"

curl --location --output $HOME/downloads/${TALOSCTL_FILENAME}-${TALOSCTL_VERSION} ${TALOSCTL_URL}
sudo install -o root -g root -m 0755 $HOME/downloads/${TALOSCTL_FILENAME}-${TALOSCTL_VERSION} /usr/local/bin/${TALOSCTL_FILENAME}-${TALOSCTL_VERSION}
sudo rm /usr/local/bin/talosctl
sudo ln /usr/local/bin/${TALOSCTL_FILENAME}-${TALOSCTL_VERSION} /usr/local/bin/talosctl
rm $HOME/downloads/${TALOSCTL_FILENAME}-${TALOSCTL_VERSION}

#--- Install 'kubectl'
export KUBECTL_VERSION="v1.26.3"
export KUBECTL_FILENAME="kubectl-${TOOLING_OS}-${TOOLING_ARCH}"
export KUBECTL_URL="https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/${TOOLING_OS}/${TOOLING_ARCH}/kubectl"

curl --location --output $HOME/downloads/${KUBECTL_FILENAME}-${KUBECTL_VERSION} ${KUBECTL_URL}
sudo install -o root -g root -m 0755 $HOME/downloads/${KUBECTL_FILENAME}-${KUBECTL_VERSION} /usr/local/bin/${KUBECTL_FILENAME}-${KUBECTL_VERSION}
sudo rm /usr/local/bin/kubectl
sudo ln /usr/local/bin/${KUBECTL_FILENAME}-${KUBECTL_VERSION} /usr/local/bin/kubectl
rm $HOME/downloads/${KUBECTL_FILENAME}-${KUBECTL_VERSION}
```

## Prep the Sidero Pi 4

* Added DHCP rule to assign IP and DNS / NTP for Pi4's MAC Address
* Used SD Card to flash Pi4 EEPROM to boot from USB
* Connected USB to SSD and flashed with Raspberry Pi Imager using Metal-Pi image from Talos
* Connected USB to SSD on Pi4 and booted from Talos Raw Image
* You're now running Talos OS on this Pi 4

```bash
export SIDERO_ENDPOINT=192.168.68.4

talosctl gen config sidero-metal https://${SIDERO_ENDPOINT}:6443/ \
		--additional-sans=sidero-metal,sidero-metal.kubernetes.lesmerises.jgarfield.com \
		--config-patch-control-plane @./talos-linux/patch-sidero-controlplane-config.yaml \
		--with-docs=false \
		--with-examples=false \
		--output-types=controlplane,talosconfig \
		--output ./_out

talosctl config merge ./_out/talosconfig
talosctl config endpoints ${SIDERO_ENDPOINT}
talosctl config nodes ${SIDERO_ENDPOINT}
talosctl kubeconfig

talosctl apply-config --insecure -n ${SIDERO_ENDPOINT} -f ./_out/controlplane.yaml

talosctl bootstrap && kubectl -n kube-system wait --for=condition=Ready --all pods && talosctl health
```

```bash
#--- See the available Repos
clusterctl config repositories
clusterctl config provider talos

# Not 100% sure about specific version here
# Add the following snippet to $HOME/.cluster-api/clusterctl.yaml
cert-manager:
  version: "v1.11.0" # https://github.com/cert-manager/cert-manager/releases

# as-of 2023-03-23 
export CLUSTER_API_VERSION=v1.3.5 # https://github.com/kubernetes-sigs/cluster-api/releases/latest/
export TALOS_BOOTSTRAP_VERSION=v0.5.6 # https://github.com/siderolabs/cluster-api-bootstrap-provider-talos/releases/latest/
export TALOS_CONTROLPLANE_VERSION=v0.4.11 # https://github.com/siderolabs/cluster-api-control-plane-provider-talos/releases/latest/
export SIDERO_INFRASTRUCTURE_VERSION=v0.5.8 # https://github.com/siderolabs/sidero/releases/latest/

export SIDERO_CONTROLLER_MANAGER_HOST_NETWORK=true
export SIDERO_CONTROLLER_MANAGER_API_ENDPOINT=${SIDERO_ENDPOINT}
export SIDERO_CONTROLLER_MANAGER_API_PORT=8081
export SIDERO_CONTROLLER_MANAGER_CONTAINER_API_PORT=8081
export SIDERO_CONTROLLER_MANAGER_SIDEROLINK_ENDPOINT=${SIDERO_ENDPOINT}
export SIDERO_CONTROLLER_MANAGER_SIDEROLINK_PORT=51821
export SIDERO_CONTROLLER_MANAGER_EXTRA_AGENT_KERNEL_ARGS=
export SIDERO_CONTROLLER_MANAGER_AUTO_ACCEPT_SERVERS=false
export SIDERO_CONTROLLER_MANAGER_AUTO_BMC_SETUP=false
export SIDERO_CONTROLLER_MANAGER_INSECURE_WIPE=true
export SIDERO_CONTROLLER_MANAGER_SERVER_REBOOT_TIMEOUT=20m
export SIDERO_CONTROLLER_MANAGER_IPMI_PXE_METHOD=uefi
export SIDERO_CONTROLLER_MANAGER_BOOT_FROM_DISK_METHOD=ipxe-sanboot

clusterctl init --core cluster-api:${CLUSTER_API_VERSION} --bootstrap talos:${TALOS_BOOTSTRAP_VERSION} --control-plane talos:${TALOS_CONTROLPLANE_VERSION} --infrastructure sidero:${SIDERO_INFRASTRUCTURE_VERSION}

# Test TFTP endpoint
curl -I http://${SIDERO_ENDPOINT}:8081/tftp/ipxe.efi
```

## References

* [Sidero on Raspberry Pi 4](https://www.sidero.dev/v0.5/guides/sidero-on-rpi4/)
* [The Cluster API Book](https://cluster-api.sigs.k8s.io/)
