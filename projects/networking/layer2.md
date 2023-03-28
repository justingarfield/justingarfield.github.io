# OSI Model - Layer 2

This page contains information related to anything specific to Layer 2 of the OSI Model.

## Table of Contents

* [Virtual Local Area Networks (VLANs)](#virtual-local-area-networks)
  * [Important Notes](#vlans-important-notes)
* [Wifi Networks](#wifi-networks)
* [Known MAC Addresses](#known-mac-addresses)

## <a id="virtual-local-area-networks"></a>Virtual Local Area Networks (VLANs)

Virtual Local Area Networks (VLANs) help split-up _broadcast domains_, and reduce the amount of chatter devices are forced to encounter on a daily basis. Broadcasts can consist of anything from a simple ICMP packet to higher-level SSDP / DLNA type media & device discovery packets.

The IEEE Standard for VLANs is: [802.1Q](https://en.wikipedia.org/wiki/IEEE_802.1Q)

### Change the Native VLAN

Make sure when you get a new switch that supports configuring VLANs, that you set all Access Ports to default to a Native VLAN other than Native VLAN of `1`.

TODO: Add the 'why'

I personally just change this to `2` and use it as more of a 'parking' VLAN for new device configuration.

### Example VLANs to use

Here are some example VLANs you may want to use on your own home network(s):

| VLAN Tag | Name | Description |
|-|-|-|
|    2 | Provisioning Network   | Used for new devices plugged into the network to get configured / setup on the default VLAN |
|    3 | Guest Network          | Used for visiting guests / temporary Internet access                                        |
|    4 | Security Network       | Used for security devices / home protection systems                                         |
|    5 | Gaming Network         | Used for gaming consoles and PCs                                                            |
|    6 | Media Network          | Used for streaming devices, media servers, etc.                                             |
|    7 | Work Network           | Used for work laptops by folks that work from home                                          |
|    8 | Lab Network            | Used for lab setups / testing with VMs, etc.                                                |

### Avoid using these VLAN Tags

In Cisco-land (and a few other vendor types), the following VLAN IDs are reserved.

| VLAN Tag | Purpose |
|-|-|
| 1    | Native VLAN - Avoid having Access Ports default to this VLAN |
| 1002 | fddi-default |
| 1003 | token-ring-default |
| 1004 | fddinet-default |
| 1005 | trnet-default |

## <a id="wifi-networks"></a>Example Wifi Networks to use

Here are some example Wifi Networks you may want to setup on your own home network(s):

| SSID | VLAN Tag | Description |
|-|-|-|
| Provisioning   | 2 | Used for new devices plugged into the network to get configured / setup on the default VLAN |
| Guest          | 3 | Used for visiting guests / temporary Internet access                                        |
| Security       | 4 | Used for security devices / home protection systems                                         |
| Gaming         | 5 | Used for gaming consoles and PCs                                                            |
| Media          | 6 | Used for streaming devices, media servers, etc.                                             |
| Work           | 7 | Used for work laptops by folks that work from home                                          |

## MAC Addresses

You should document all of your devices' MAC Addresses to have as a reference when debugging, viewing Wifi logs, adding DHCP Static rules, etc.

It's as simple as creating a table like so to record them:

| Device | MAC Address | Description |
|-|-|-|
| Work Laptop Wired | AB:CD:EF:G1:23:45 | Work Laptop's Wired Interface |
| Work Laptop Wifi  | 45:32:1G:FE:DC:BA | Work Laptop's Wifi Interface  |

## Troubleshooting

### Wifi Traffic not appearing across VLAN Trunk

If you have a Wifi AP that supports VLANs, and are plugging it into a trunk port on one of your switches, but you can't see any traffic coming from a particular VLAN, make sure you add the VLAN to the switch with the trunk port, even if you don't have any wired interfaces on the VLAN. Some switches will not pass VLAN traffic unless there's an entry in the switch representing the VLAN (even if it's a trunk port).
