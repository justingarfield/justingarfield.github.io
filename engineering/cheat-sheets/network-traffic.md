# Network Traffic

## Multicast Madness

| Protocol | Src Address | Src Port | Dst Address | Dst Port | Description |
|-|-|-|-|-|-|
| UDP      | *           | *        | 224.0.0.251 |     5353 | [Multicast DNS](https://en.wikipedia.org/wiki/Multicast_DNS) |
|  |      |      |  |  |  |

## Kubernetes

| Protocol | Port | Description |
|-|-|-|
| TCP      |  2379 | etcd Client communications |
| TCP      |  2380 | etcd Server-to-Server communications |
| TCP      |  6443 | Kubernetes API Server |
| TCP      |  8081 | Sidero iPXE, metadata and gRPC service |
| UDP      |    69 | Sidero TFTP service |
| UDP      | 51821 | SideroLink Wireguard service |

## Device Specific


## Application Specific


## References

* [Multicast address](https://en.wikipedia.org/wiki/Multicast_address)
* [TCP and UDP ports used by Apple software products](https://support.apple.com/en-us/HT202944)
