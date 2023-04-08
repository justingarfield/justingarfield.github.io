# Projects -> Kubernetes -> opnSense Configuration

In order to make my [Sidero Metal](https://www.sidero.dev/) and [Workload Cluster](https://www.sidero.dev/v0.5/getting-started/create-workload/) control planes highly available, I rely on a few features of my opnSense firewalls.

I could have used [MetalLB](https://metallb.org/) in my environment, but since I'm using Raspberry Pi 4s for the control planes, I decided to use nginx on my beefier firewalls since they're not doing a whole lot.

_For an in-depth look at how my firewalls are configured for HA, take a look at my [networking project page](#)_

## Virtual IP Addresses

opnSense uses CARP (FreeBSD version of VRRP) to handle Virtual IP (VIP) addresses in a highly available environment.

I've added the following two CARP VIPs in my environment, one for each control plane involved.

| Name | Description |
|-|-|
| Sidero Metal Control Plane VIP     | Used as the VIP for Sidero Metal control plane API Server traffic / Sidero services |
| Workload Cluster Control Plane VIP | Used as the VIP for workload cluster control plane API Server traffic |

## nginx

I was originally going to use HA Proxy, since that's what I learned to build HA K8s setups with already; but with addition of Sidero and UDP for TFTP traffic, I ended up going with nginx since it can load balance UDP out-of-the-box. 

_(HA Proxy can use something called ALOHA for UDP I believe, but that's not something that's easily available in opnSense distributions)_

### Installing

* Navigate to *System* -> *Firmware* -> *Status*
* Click **Check for updates** button
  * Update system if needed
* Navigate to *System* -> *Firmware* -> *Plugins*
* Install `os-nginx`
* **Refresh** Web GUI _(this allows the new nginx entry under Services to appear)_

### Configuration

#### Upstream Servers

Navigate to *Services* -> *Nginx* -> *Configuration* -> *Upstream* -> *Upstream Server*

| Description                        | Port | Server Priority |
|-|-|-|
| Sidero Metal - TFTP - Node 01      |   69 |               1 |
| Sidero Metal - TFTP - Node 02      |   69 |               1 |
| Sidero Metal - TFTP - Node 03      |   69 |               1 |
| Sidero Metal - APIServer - Node 01 | 6443 |               1 |
| Sidero Metal - APIServer - Node 02 | 6443 |               1 |
| Sidero Metal - APIServer - Node 03 | 6443 |               1 |
| Workload Cluster - APIServer - Node 01 | 6443 |               1 |
| Workload Cluster - APIServer - Node 02 | 6443 |               1 |
| Workload Cluster - APIServer - Node 03 | 6443 |               1 |

#### Upstreams

Navigate to *Services* -> *Nginx* -> *Configuration* -> *Upstream* -> *Upstream*

| | | |
|-|-|-|
| | | |

#### Stream Servers

Navigate to *Services* -> *Nginx* -> *Configuration* -> *Data Streams* -> *Stream Servers*

| Listen Address                          | UDP Port | Upstream Servers |
|-|-|-|
| `<Sidero Metal Control Plane VIP>:69`   | Checked  | Sidero Metal - TFTP |

#### Locations

Navigate to *Services* -> *Nginx* -> *Configuration* -> *HTTP(S)* -> *Location*

| Description | URL Pattern | Upstream Servers |
|-|-|-|
| Sidero Metal - APIServer | `/` | Sidero Metal - APIServer |

#### HTTP Servers

Navigate to *Services* -> *Nginx* -> *Configuration* -> *HTTP(S)* -> *HTTP Server*

| HTTP Listen Address | Server name | Locations |
|-|-|-|
| `<Sidero Metal Control Plane VIP>:6443`     | `<Sidero Metal Control Plane VIP>` |  |
| `<Workload Cluster Control Plane VIP>:6443` | `<Workload Cluster Control Plane VIP>` | |

### Enable Service

Navigate to *Services* -> *Nginx* -> *Configuration* -> *General Settings*

* **Check** `Enable nginx`
* **Click** `Apply`

## Firewall Rules

| | | | |
|-|-|-|-|
