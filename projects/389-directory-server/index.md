# 389 Directory Server

## Sizing

* Basing on 10k to 500k entries
* Looking at around 13GB Disk Space (entries + tmpfs + 10% for replication changelog)
* Looking at around 7GB for Memory (entries + replication + db cache + DN cache + NDN cache)

### Disk

* With enabled replication, its metadata can require up to 10% more of the total disk space.
* A replication changelog with 1 million changes can add at least 315 MB to the total disk space requirement.
* The temporary file system (tmpfs) mounted in /dev/shm/ should have at least 4 GB of available space to store RHDS temporary files.

### Memory

* Make sure your system has enough RAM available to keep the entire database in cache.
* The required RAM size can be higher than the recommended one depending on server configuration and usage patterns.

## References

* [389 12.1 Hardware Reqs](https://access.redhat.com/documentation/en-us/red_hat_directory_server/12/html/red_hat_directory_server_12_release_notes/assembly_general-information_rhds-12-rn#ref_general-hardware-requirements_assembly_general-information)
* []()
