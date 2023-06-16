---
layout: post
title: blah
---

## TL;DR

```shell
vboxmanage modifyvm <vm name> --hpet on
```

## Content

I was recently building-out a lab environment using VirtualBox, Talos Linux / Kubernetes, and Windows 11 Professional.

My control plane nodes kept constantly locking-up and doing really weird stuff that I had never witnessed before in other setups (microk8s, physical hardware, minikube), regardless of how many combinations of provisioning the cluster I tried.

Digging into the VirtualBox logs for one of the Control Plane nodes, I noticed a lot of warnings about "CPU Stall".

After doing some searching, I found the solution at https://www.virtualbox.org/ticket/20131#comment:2.

It was literally as simple as:

1. Shut down the VMs
2. Turn on HPET using the VBoxManage utility: `vboxmanage modifyvm <vm name> --hpet on` 
3. Start your VMs back up _(Depending on how messed-up your VMs got prior, you may have to wipe them clean and start fresh with this new setting)_

Even though that post was for an AMD Ryzen CPU, it worked all the same in fixing things on my Intel machine.

According to the [official VirtualBox documentation](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/settings-system.html#settings-processor): `--hpet on|off: Enables and disables a High Precision Event Timer (HPET) which can replace the legacy system timers. This is turned off by default. Note that Windows supports a HPET only from Vista onwards.`

## Notes

* According to some forum posts I read, turning this on can negatively impact gaming performance...YMMV
