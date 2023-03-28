# OSI Model - Layer 1

This page contains information related to anything specific to Layer 1 of the OSI Model.

## Cable/Wiring types in-use

### Cat7A

I personally decided to use Cat7A as the minimum cable standard for anything I ran in my house. Why?...

* With the amount of hardware I have running all throughout the house, I just didn't want to chance it with EMI of any sort (especially for things like the device that's hard-wired to my electrical box to measure telemetry from the entire electrical bus)
* I have some PoE+ devices running which can introduce a bit of EMI due-to over 30W of draw per device
* Future-proofing. I currently have four devices using 10Gbps connections due to 4k content, video editing, batch transcoding, etc. Who knows what else I'll need 10Gbps on down-the-road.

#### Useful resources

* [What Is Cat7A Cable? The Ultimate Guide](https://cat6bulkcable.com/what-is-cat7a-cable/)
* [Wikipedia - ISO/IEC 11801](https://en.wikipedia.org/wiki/ISO/IEC_11801#CAT7)
* [Elite CAT7A Shielded Riser (CMR), Ethernet Cable 1000ft Reel](https://www.amazon.com/gp/product/B072N8SYWJ)

### CAT8

CAT8 is being used to handle any connectivity that deals with massive amounts of data transfer between devices. I may upgrade to 40Gbps down-the-road when I get my hands on a board with PCI-x V5.

Currently used for...

* Between my main Plex Media Server / Video Editing / Transcoding machine and a 10Gbps GBIC in my core switch
* All patch cables in-house
* All connections from servers / high-availability related resources to core switch

#### Useful resources

* [Wikipedia - ISO/IEC 11801](https://en.wikipedia.org/wiki/ISO/IEC_11801#CAT8)
* [LINKUP - Cat8 Ethernet Cable S/FTP](https://www.amazon.com/gp/product/B07JQ4BV3T)
* [Cable Matters Easy Crimp Tool](https://www.amazon.com/gp/product/B074HG3VW3)
* [FS - Cat8 Patch Cords](https://www.fs.com/c/cat8-patch-cords-3371)

## Connector types in-use

### Shielded 8P8C connectors

[Cable Matters 5-Pack Shielded RJ45 Cat8 Keystone Jack](https://www.amazon.com/gp/product/B074HH9RHW)
