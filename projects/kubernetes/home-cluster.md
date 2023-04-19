---
layout: projects
title: My Home Kubernetes Cluster
---

## Purpose

Over the years as I've been building out my at-home software stack, it has become increasingly hard to keep track of all the software in-use, versioning, moving containers around to different hardware I can afford to run, etc.

Since I've been wanting to shift more to Ops vs. Software Engineering lately, I decided to spend some time trying out different combinations of Kubernetes solutions. I tested things ranging from single-node Microk8s / Minikube to K3s to VMs vs. giant-ass HP DL380s, and everything in-between _(within the context of hardware you would find a person's home...haven't scaled to anything above 5-nodes total yet)_.

At first I was going to use some old HP DL380s as worker nodes, but right about the time I started this whole project, prices went up immenseley on electricity in my area as well; so I decided to shutdown the DL380s for now and have switched to a Raspberry Pi 4 Control Plane and my old beefy-ass desktop as just a single worker node (for now).

Since I can see myself introducing different hardware over time (probably buy some Intel NUCs or SuperMicro Servers next for worker nodes), I wanted something that's repeatable at the bare-metal level of things, without having to be running a giant-ass hyper-visor solution. Since I only plan on containerizing everything going forward, I decided I didn't need to support anything but Kubernetes at this point in time.

<blurb about trial-and suffering of custom solution??>

Between the bare-metal aspect of things, wanting minimal configuration, minimal attack surface, etc. I ended up at the wonderful Sidero Metal / Talos Linux projects.

## Nodes

| Hostname | Type | Description |
|-|-|-|
| sidero-metal | Single Node Control Plane | Raspberry Pi 4 Model B |
| cp01 | Control Plane Node | Raspberry Pi 4 Model B |
| cp02 | Control Plane Node | Raspberry Pi 4 Model B |
| cp03 | Control Plane Node | Raspberry Pi 4 Model B |
| wk01 | Worker Plane Node | Custom PC w/ 36-core Xeon and 128GB RAM |

## Tools

* talosctl
* kubectl
* clusterctl
