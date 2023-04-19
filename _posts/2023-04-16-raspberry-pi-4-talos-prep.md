---
layout: post
title: Talos v1.4 Prep for Raspberry Pi 4B
tags: talos raspberry-pi kubernetes
---

# {{ page.title }}

## Overview

If you saw [my post about the UCTronics Pi Rackmount Kit I picked up](#), I'm using that same hardware to build-out a Talos Linux Kubernetes cluster for home use.

This post covers how I installed Talos Linux v1.4 onto the Pi 4 USB-SSDs. While the process is relatively simple, I still wanted to share the steps and include a little bit more information re: my experience with Sidero and Pis.

## Sidero

## DNS Records

| host | Domain | Type | Value | Description |
|-|-|-|-|-|
| talos-endpoints | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.8 | Used to load balance traffic across the Talos Endpoints _(not nodes)_. |
| cp   | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.8 | Used to load balance traffic across the Kubernetes API Server on CP nodes. |
| cp01 | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.9 | Kubernetes Control Plane Node 01 |
| cp02 | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.10 | Kubernetes Control Plane Node 02 |
| cp03 | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.11 | Kubernetes Control Plane Node 03 |
| wk01 | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.50 | Kubernetes Worker Plane Node 01 |
| wk02 | k8s.mydomain.tld | A (IPv4 address) | 192.168.22.51 | Kubernetes Worker Plane Node 02 |

## Firewall Rules

## HA Proxy Load Balancer

### Real Servers

| Name or Prefix | Description | FQDN or IP |
|-|-|-|
| node-cp01 | Kubernetes Control Plane Node 01 | 192.168.22.9 |
| node-cp02 | Kubernetes Control Plane Node 02 | 192.168.22.10 |
| node-cp03 | Kubernetes Control Plane Node 03 | 192.168.22.11 |
| node-wk01 | Kubernetes Worker Plane Node 01 | 192.168.22.50 |
| node-wk02 | Kubernetes Worker Plane Node 02 | 192.168.22.51 |

### Health Monitors

| Name | Description | Check type | Port to check |
|-|-|-|-|
| Kubernetes Control Plane | Kubernetes Control Plane | TCP | 6443 |
| Talos Endpoints | Talos Endpoints | TCP | 50000 |

### Backend Pools

| Name | Description | Mode | Balancing Algorithm | Servers | Enable Health Checking | Health Monitor |
|-|-|-|:-:|:-:|:-:|-|
| KubernetesControlPlane | Kubernetes Control Plane | TCP (Layer 4) | Round Robin | node-cp01<br />node-cp02<br />node-cp03 | checked | Kubernetes Control Plane |
| TalosEndpoints | Talos Endpoints | TCP (Layer 4) | Round Robin | node-cp01<br />node-cp02<br />node-cp03 | checked | Talos Endpoints |

### Public Pools

| Public Service Name | Description | Listen Addresses | Type | Default Backend Pool |
|-|-|-|-|-|
| KubernetesControlPlane | Kubernetes Control Plane | 192.168.22.8:6443 | TCP | KubernetesControlPlane |
| TalosEndpoints | Talos Endpoints | 192.168.22.8:50000 | TCP | TalosEndpoints |
