# OSI Model - Layer 3

This page contains information related to anything specific to Layer 3 of the OSI Model.

## Table of Contents

* [Background and Learning](#background-and-learning)
  * [Classless inter-domain routing (CIDR)](#classless-inter-domain-routing)
  * [IPv4 Private Address Ranges](#private-address-ranges)
  * [192.168.0.0/16 Private Address Space](#private-address-space)
* [Planning](#planning)
* [IP Subnet Ranges](#ip-subnet-ranges)
* [IP Subnet Allocations](#ip-subnet-allocations)
* [Networks and IP Address Assignments](#ip-address-assignments)
  * [OOTB device defaults Network](#ootb-device-defaults-network)
  * [Guest Network](#guest-network)
  * [Kubernetes Network](#kubernetes-network)
  * [Security Network](#security-network)
  * [Storage Network](#storage-network)
  * [Infrastructure Network](#infrastructure-network)
  * [Management Network](#management-network)
  * [Apple Devices Network](#apple-devices-network)
  * [Office Network](#office-network)
  * [Synology NAS Network](#synology-nas-network)
  * [AnyCubic Resin Printer Network](#anycubic-resin-printer-network)
  * [Samsung Smart TV Network](#samsung-smart-tv-network)
  * [Brother AIO Printer Network](#brother-aio-printer-network)
  * [Work Laptop Network](#work-laptop-network)
  * [Microsoft XBox Network](#microsoft-xbox-network)
  * [Nintendo Switch Network](#nintendo-switch-network)

## <a id="#background-and-learning"></a>Background and Learning

In order to follow along on this page, there are certain concepts that must be understood first.

### <a id="#classless-inter-domain-routing"></a>Classless inter-domain routing (CIDR)

In-case you're not familiar with CIDR format, `/24` is the same as saying something has a Subnet Mask of `255.255.255.0`, where-as something with a `/16` would be a Subnet Mask of `255.255.0.0`...16-bits vs. 24-bits of the IP Address used for the network identifier portion.

[See also](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

### <a id="#private-address-ranges"></a>IPv4 Private Address Ranges

Address ranges below are reserved by [IANA](https://www.iana.org/) for private intranets, and not routable to the Internet. For additional information, see [RFC 1918](https://www.rfc-editor.org/rfc/rfc1918).

| CIDR | Netmask | IP Range Start | IP Range End | Description |
|-|-|-|-|-|
| `10.0.0.0/8`     | `255.0.0.0`   | `10.0.0.0`    | `10.255.255.255`  | Used to be referred to as a Class A address space |
| `169.254.0.0/16` | `255.255.0.0` | `169.254.0.1` | `169.254.255.254` | APIPA address space                               |
| `172.16.0.0/12`  | `255.240.0.0` | `172.16.0.0`  | `172.31.255.255`  | Used to be referred to as a Class B address space |
| `192.168.0.0/16` | `255.255.0.0` | `192.168.0.0` | `192.168.255.255` | Used to be referred to as a Class C address space |

### <a id="private-address-space"></a>192.168.0.0/16 Private Address Space

Let's focus on the most commonly found private address space, which is `192.168.0.0/16`. Most home users will recognize this address range, as almost every home router, IoT device, etc. provision OOTB with an address on this network.

This private address range provides **65,534** usable host addresses before doing any subnetting. For a single home, that's quite a lot of IP Addresses.

For most folks, you purchase a router device from a company like Netgear or Linksys/Cisco, plug in the WAN port to your ISP's CPE (Cable Modem, Fiber Termination Point, etc.), and then all your local devices to the other ports. OOTB these routers usually have a default network of `192.168.0.0/24` or `192.168.1.0/24`. 

These `192.168.x.x/24` networks sit inside of the much larger `192.168.0.0/16` private address space...This means we can screw with this entire address range as much as we want when it comes to subnetting. By subnetting the crap out of this chunk of addresses, we can utilize our address space much more efficiently, reduce network chatter, and isolate devices that might be sketchy or require more scrutiny that others.

Note: One thing to keep in-mind, as the amount of subnets increases, the amount of interfaces and routes will increase as well (something has to route between subnets). For some folks, this may be virtual interfaces; others, additional PCIx/USB adapters. In my case, my opnSense firewalls act as the inter-VLAN/subnet routing devices.

## <a id="planning"></a>Planning

Planning is key to subnetting your available address space decently. One of the most simple starting points is to simply list out the requirements you have, and start from there. For me personally, those requirements were...

* The ability to isolate certain devices on their own VLANs / Wifi Network, so that they can't send/receive broadcasts to anyone else
* Minimum of 6 usable host addresses on each subnet
  * Three for the gateways (VIP + Static IP for each Firewall)
  * Two for a device itself (in case it has both Wired and Wireless)
  * One for allocating to a diagnostic tool / laptop for debugging
* A nice giant, contiguous block for my Kubernetes Nodes and Apps
* Place firewall replication traffic on its own isolated private range (outside of the `192.168.0.0/16` space)
* Place firewall public-facing interfaces on their own isolated private range (outside of the `192.168.0.0/16` space)
* Make the most out of the available address space, and try to do some "future-proofing" to avoid large-scale changes later

## <a id="ip-subnetting-scheme"></a>IP Subnetting Scheme

I decided to go with the following subnetting scheme for my own house. Your mileage may vary, but this should cover almost every use-case, and then some for a single residence.

| Count | Net Size | Description |
|-|-|-|
|   1 | `/22` | 1022 hosts; for Kubernetes Cluster and Services |
|  72 | `/24` | 254 hosts; what we're normally used to with 192.168.x.x subnets |
| 128 | `/25` | 126 hosts; little smaller, but still a very viable network size |
| 256 | `/26` | 62 hosts; little smaller, but still a viable network size |
| 255 | `/27` | 30 hosts; middle-ground network size, maybe use for assigning rooms of a house? |
| 256 | `/28` | 14 hosts; good for small labs, or maybe a small group of like devices |
| 256 | `/29` | 6 hosts; use for maybe a printer pool or something? |
