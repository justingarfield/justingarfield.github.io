# 

TODO: Add registry settings for user/pass with secrets so we can keep Repo private

This is an elaboration on the instructions found at [Documentation / Advanced Guides / Developing Talos](https://www.talos.dev/v1.3/advanced/developing-talos/) and [Documentation / Guides / Raspberry Pi4 as Servers](https://www.sidero.dev/v0.5/guides/rpi4-as-servers/).

## Pre-reqs

* I assume that you're using Docker Desktop on Windows 11 Pro with the Windows Subsystem for Linux (WSL).
* A logged-in GitHub account / GitHub CLI

## Some background

One of the neat things about Talos Linux and Sidero, are that they're built using an environment that's completely containerized! With just some basics installed (Docker Desktop), you can pretty much build anything related to Talos or Sidero. To allow for this, one important thing to ensure is working properly is the newer methodology of working with and creating container images: buildx.

The easiest way to interact with, and ensure a correct versions of, buildx is to just use it in a container.

```bash
docker buildx create --driver docker-container --driver-opt network=host --name entitlement.security.insecure --buildkitd-flags '--allow-insecure-entitlement security.insecure' --use
```

## The Process

Create a new fork of the [siderolabs/pkgs](https://github.com/siderolabs/pkgs) repository. I named my fork `siderolabs-pkgs` vs. just `pkgs`.

Note: You only need to fork the `main` branch.

```bash
export GH_USERNAME=<your gh username>
mkdir -p $HOME/src/$GH_USERNAME
cd $HOME/src/$GH_USERNAME
gh repo clone <your gh fork>
cd <cloned repo dir>

... add pi4 files ...
... add to Pkgfile ...
... add to Makefile ...

PLATFORM=linux/arm64 make raspberrypi4-uefi

docker login ghcr.io --username $GH_USERNAME
```

If you get a warning of:
```
WARNING! Your password will be stored unencrypted in /home/jgarfield/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```

then add to ~/.docker/config.json

```json
{
  "credsStore": "desktop.exe"
}
```

```bash
PLATFORM=linux/arm64 USERNAME=justingarfield PUSH=true TAG=v1.3.0-2 make raspberrypi4b-uefi

kubectl -n sidero-system patch deployments.apps sidero-controller-manager --patch "$(cat patch-tftp-raspberrypi4b-uefi.yaml)"
```

Will most likely have to scale-down old pod due to port conflict...

```bash
kubectl -n sidero-system get pods

sidero-controller-manager-6f5fc67558-msgrh   0/4     Pending   0          2m20s
sidero-controller-manager-f6877bd5c-lxhgs    4/4     Running   0          14h
```

```bash
kubectl -n sidero-system describe pods/<your pod name>

Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  45s   default-scheduler  0/1 nodes are available: 1 node(s) didn't have free ports for the requested pod ports. preemption: 0/1 nodes are available: 1 No preemption victims found for incoming pod..
```

```bash
kubectl -n sidero-system scale deployments/sidero-controller-manager --replicas=0
kubectl -n sidero-system scale deployments/sidero-controller-manager --replicas=1

curl -I http://${SIDERO_ENDPOINT}:8081/tftp/61a90587/config.txt
```

## repo visilibity

* `sidero-controller-manager-6b65f84f76-nx676   0/4     Init:ImagePullBackOff   0             2m29s`

## Boot first Pi 4 over network

Create a new ServerClass to apply to the Pi 4s that network boot

```bash
kubectl apply -f serverclass-pi4b-controlplane.yaml
```

* Boot Pi 4
* Once Registered and continually rebooting

```bash
kubectl get servers -o wide

NAME                                   HOSTNAME   BMC IP   ACCEPTED   CORDONED   ALLOCATED   CLEAN   POWER   AGE
00d03114-0000-0000-0000-e45f0128c8fe   cp01                false                                     on      4m32s

kubectl patch server 00d03114-0000-0000-0000-e45f0128c8fe --type='json' -p='[{"op": "replace", "path": "/spec/accepted", "value": true}]'
kubectl patch server 00d03114-0000-0000-0000-e45f012f2599 --type='json' -p='[{"op": "replace", "path": "/spec/accepted", "value": true}]'
```

## 

```bash
export CONTROL_PLANE_SERVERCLASS=rpi4-d03114
export WORKER_SERVERCLASS=any
export TALOS_VERSION=v1.3.6
export KUBERNETES_VERSION=v1.26.3
export CONTROL_PLANE_PORT=6443
export CONTROL_PLANE_ENDPOINT=192.168.68.8
export SIDERO_INFRASTRUCTURE_VERSION=v0.5.8 # https://github.com/siderolabs/sidero/releases/latest/

clusterctl generate cluster hybrid --infrastructure sidero:${SIDERO_INFRASTRUCTURE_VERSION} > ./sidero-metal/clusters/hybrid.yaml

kubectl apply -f ./sidero-metal/clusters/hybrid.yaml

watch kubectl --context=admin@sidero-metal get servers,machines,clusters
```

## Pi 4 Notes

* [Raspberry Pi 4 Boot EEPROM](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-eeprom)
* `bootcode.bin` is not used on the Raspberry Pi 4, because it has been replaced by boot code in the [onboard EEPROM](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-eeprom).

There is a Health Probe that hits `2023/03/22 14:44:54 HTTP GET /boot.ipxe 127.0.0.1:50144` every 10-seconds
https://github.com/siderolabs/sidero/blob/v0.5.8/app/sidero-controller-manager/internal/ipxe/ipxe_server.go#L556

----

iPXE Template that's setting the `kernel` / `initramfs.xz` and calling boot here: https://github.com/siderolabs/sidero/blob/v0.5.8/app/sidero-controller-manager/internal/ipxe/ipxe_server.go#L98

iPXE Template for `ipxe-exit`: https://github.com/siderolabs/sidero/blob/v0.5.8/app/sidero-controller-manager/internal/ipxe/ipxe_server.go#L105

iPXE Template for `ipxe-sanboot`: https://github.com/siderolabs/sidero/blob/v0.5.8/app/sidero-controller-manager/internal/ipxe/ipxe_server.go#L110

`/env/agent-arm64/vmlinuz` ends up mapping to `/var/lib/sidero/env/agent-arm64/vmlinuz` which comes from https://github.com/siderolabs/sidero/blob/v0.5.8/Dockerfile#L210 which points to https://github.com/siderolabs/sidero/blob/v0.5.8/Dockerfile#L24

`/env/agent-arm64/initramfs.xz` ends up mapping to `/var/lib/sidero/env/agent-arm64/initramfs.xz` which comes from https://github.com/siderolabs/sidero/blob/v0.5.8/Dockerfile#L208 which points to https://github.com/siderolabs/sidero/blob/v0.5.8/Dockerfile#L186

Raspberry Pi 4 - Boot EEPROM: https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-eeprom
Raspberry Pi 4 - Boot Flow: https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-flow
Raspberry Pi 4 - The boot Folder: https://www.raspberrypi.com/documentation/computers/configuration.html#the-boot-folder
