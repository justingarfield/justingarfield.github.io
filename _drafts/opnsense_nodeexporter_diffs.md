## Current Time Statistics

```
node_scrape_collector_duration_seconds{collector="time"} 3.9486e-05
node_scrape_collector_success{collector="time"} 1
# HELP node_time_seconds System time in seconds since epoch (1970).
# TYPE node_time_seconds gauge
node_time_seconds 1.689119165245854e+09
# HELP node_time_zone_offset_seconds System time zone offset in seconds.
# TYPE node_time_zone_offset_seconds gauge
node_time_zone_offset_seconds{time_zone="UTC"} 0
```

## NTP Statistics

```
# HELP node_ntp_leap NTPD leap second indicator, 2 bits.
# TYPE node_ntp_leap gauge
node_ntp_leap 0
# HELP node_ntp_offset_seconds ClockOffset between NTP and local clock.
# TYPE node_ntp_offset_seconds gauge
node_ntp_offset_seconds -6.9619e-05
# HELP node_ntp_reference_timestamp_seconds NTPD ReferenceTime, UNIX timestamp.
# TYPE node_ntp_reference_timestamp_seconds gauge
node_ntp_reference_timestamp_seconds 1.6891188234527218e+09
# HELP node_ntp_root_delay_seconds NTPD RootDelay.
# TYPE node_ntp_root_delay_seconds gauge
node_ntp_root_delay_seconds 0.000518799
# HELP node_ntp_root_dispersion_seconds NTPD RootDispersion.
# TYPE node_ntp_root_dispersion_seconds gauge
node_ntp_root_dispersion_seconds 0.020278931
# HELP node_ntp_rtt_seconds RTT to NTPD.
# TYPE node_ntp_rtt_seconds gauge
node_ntp_rtt_seconds 0.000325898
# HELP node_ntp_sanity NTPD sanity according to RFC5905 heuristics and configured limits.
# TYPE node_ntp_sanity gauge
node_ntp_sanity 1
# HELP node_ntp_stratum NTPD stratum.
# TYPE node_ntp_stratum gauge
node_ntp_stratum 2
node_scrape_collector_success{collector="ntp"} 1
```

## Memory Statistics

```
# HELP node_memory_active_bytes Recently used by userland
# TYPE node_memory_active_bytes gauge
node_memory_active_bytes 6.57883136e+08
# HELP node_memory_buffer_bytes Disk IO Cache entries for non ZFS filesystems, only usable by kernel
# TYPE node_memory_buffer_bytes gauge
node_memory_buffer_bytes 7.11254016e+08
# HELP node_memory_cache_bytes Almost free, backed by swap or files, available for re-allocation
# TYPE node_memory_cache_bytes gauge
node_memory_cache_bytes 0
# HELP node_memory_free_bytes Unallocated, available for allocation
# TYPE node_memory_free_bytes gauge
node_memory_free_bytes 2.765426688e+09
# HELP node_memory_inactive_bytes Not recently used by userland
# TYPE node_memory_inactive_bytes gauge
node_memory_inactive_bytes 3.536576512e+09
# HELP node_memory_laundry_bytes Dirty not recently used by userland
# TYPE node_memory_laundry_bytes gauge
node_memory_laundry_bytes 0
# HELP node_memory_size_bytes Total physical memory size
# TYPE node_memory_size_bytes gauge
node_memory_size_bytes 8.24469504e+09
# HELP node_memory_swap_in_bytes_total Bytes paged in from swap devices
# TYPE node_memory_swap_in_bytes_total counter
node_memory_swap_in_bytes_total 0
# HELP node_memory_swap_out_bytes_total Bytes paged out to swap devices
# TYPE node_memory_swap_out_bytes_total counter
node_memory_swap_out_bytes_total 0
# HELP node_memory_swap_size_bytes Total swap memory size
# TYPE node_memory_swap_size_bytes gauge
node_memory_swap_size_bytes 8.589893632e+09
# HELP node_memory_swap_used_bytes Currently allocated swap
# TYPE node_memory_swap_used_bytes gauge
node_memory_swap_used_bytes 0
# HELP node_memory_user_wired_bytes Locked in memory by user, mlock, etc
# TYPE node_memory_user_wired_bytes gauge
node_memory_user_wired_bytes 0
# HELP node_memory_wired_bytes Locked in memory by kernel, mlock, etc
# TYPE node_memory_wired_bytes gauge
node_memory_wired_bytes 1.319985152e+09
node_scrape_collector_success{collector="meminfo"} 1
```

## Network Interface Statistics

```
# HELP node_network_receive_bytes_total Network device statistic receive_bytes.
# TYPE node_network_receive_bytes_total counter
node_network_receive_bytes_total{device="bge0"} 68839
node_network_receive_bytes_total{device="bge1"} 2.227572996e+09
node_network_receive_bytes_total{device="enc0"} 0
node_network_receive_bytes_total{device="ixl0"} 0
node_network_receive_bytes_total{device="ixl1"} 0
node_network_receive_bytes_total{device="ixl2"} 1.995327708e+09
node_network_receive_bytes_total{device="ixl3"} 2.95070981e+08
node_network_receive_bytes_total{device="lagg0"} 0
node_network_receive_bytes_total{device="lagg1"} 2.290398689e+09
node_network_receive_bytes_total{device="lo0"} 1.00744454e+08
node_network_receive_bytes_total{device="pflog0"} 0
node_network_receive_bytes_total{device="pfsync0"} 0
node_network_receive_bytes_total{device="vlan0.0002"} 0
node_network_receive_bytes_total{device="vlan0.0003"} 0
node_network_receive_bytes_total{device="vlan0.0060"} 3.2967106e+08
node_network_receive_bytes_total{device="vlan0.0064"} 0
node_network_receive_bytes_total{device="vlan0.0068"} 1.241153e+06
node_network_receive_bytes_total{device="vlan0.0665"} 1.885093e+06
node_network_receive_bytes_total{device="vlan0.0666"} 1.800764768e+09
node_network_receive_bytes_total{device="vlan0.4087"} 0
node_network_receive_bytes_total{device="vlan0.4088"} 6.2164641e+07
node_network_receive_bytes_total{device="vlan0.4089"} 0
node_network_receive_bytes_total{device="vlan0.4090"} 0
node_network_receive_bytes_total{device="vlan0.4091"} 0
node_network_receive_bytes_total{device="vlan0.4092"} 0
node_network_receive_bytes_total{device="vlan0.4093"} 0
node_network_receive_bytes_total{device="vlan0.4094"} 0
node_network_receive_bytes_total{device="wg1"} 0
# HELP node_network_receive_drop_total Network device statistic receive_drop.
# TYPE node_network_receive_drop_total counter
node_network_receive_drop_total{device="bge0"} 0
node_network_receive_drop_total{device="bge1"} 0
node_network_receive_drop_total{device="enc0"} 0
node_network_receive_drop_total{device="ixl0"} 4.294966853e+09
node_network_receive_drop_total{device="ixl1"} 4.294966852e+09
node_network_receive_drop_total{device="ixl2"} 48587
node_network_receive_drop_total{device="ixl3"} 4.294966916e+09
node_network_receive_drop_total{device="lagg0"} 8.589933705e+09
node_network_receive_drop_total{device="lagg1"} 4.295015503e+09
node_network_receive_drop_total{device="lo0"} 0
node_network_receive_drop_total{device="pflog0"} 0
node_network_receive_drop_total{device="pfsync0"} 0
node_network_receive_drop_total{device="vlan0.0002"} 0
node_network_receive_drop_total{device="vlan0.0003"} 0
node_network_receive_drop_total{device="vlan0.0060"} 0
node_network_receive_drop_total{device="vlan0.0064"} 0
node_network_receive_drop_total{device="vlan0.0068"} 0
node_network_receive_drop_total{device="vlan0.0665"} 0
node_network_receive_drop_total{device="vlan0.0666"} 0
node_network_receive_drop_total{device="vlan0.4087"} 0
node_network_receive_drop_total{device="vlan0.4088"} 0
node_network_receive_drop_total{device="vlan0.4089"} 0
node_network_receive_drop_total{device="vlan0.4090"} 0
node_network_receive_drop_total{device="vlan0.4091"} 0
node_network_receive_drop_total{device="vlan0.4092"} 0
node_network_receive_drop_total{device="vlan0.4093"} 0
node_network_receive_drop_total{device="vlan0.4094"} 0
node_network_receive_drop_total{device="wg1"} 0
# HELP node_network_receive_errs_total Network device statistic receive_errs.
# TYPE node_network_receive_errs_total counter
node_network_receive_errs_total{device="bge0"} 0
node_network_receive_errs_total{device="bge1"} 977
node_network_receive_errs_total{device="enc0"} 0
node_network_receive_errs_total{device="ixl0"} 0
node_network_receive_errs_total{device="ixl1"} 0
node_network_receive_errs_total{device="ixl2"} 0
node_network_receive_errs_total{device="ixl3"} 0
node_network_receive_errs_total{device="lagg0"} 0
node_network_receive_errs_total{device="lagg1"} 0
node_network_receive_errs_total{device="lo0"} 0
node_network_receive_errs_total{device="pflog0"} 0
node_network_receive_errs_total{device="pfsync0"} 0
node_network_receive_errs_total{device="vlan0.0002"} 0
node_network_receive_errs_total{device="vlan0.0003"} 0
node_network_receive_errs_total{device="vlan0.0060"} 0
node_network_receive_errs_total{device="vlan0.0064"} 0
node_network_receive_errs_total{device="vlan0.0068"} 0
node_network_receive_errs_total{device="vlan0.0665"} 0
node_network_receive_errs_total{device="vlan0.0666"} 0
node_network_receive_errs_total{device="vlan0.4087"} 0
node_network_receive_errs_total{device="vlan0.4088"} 0
node_network_receive_errs_total{device="vlan0.4089"} 0
node_network_receive_errs_total{device="vlan0.4090"} 0
node_network_receive_errs_total{device="vlan0.4091"} 0
node_network_receive_errs_total{device="vlan0.4092"} 0
node_network_receive_errs_total{device="vlan0.4093"} 0
node_network_receive_errs_total{device="vlan0.4094"} 0
node_network_receive_errs_total{device="wg1"} 0
# HELP node_network_receive_multicast_total Network device statistic receive_multicast.
# TYPE node_network_receive_multicast_total counter
node_network_receive_multicast_total{device="bge0"} 266
node_network_receive_multicast_total{device="bge1"} 59
node_network_receive_multicast_total{device="enc0"} 0
node_network_receive_multicast_total{device="ixl0"} 0
node_network_receive_multicast_total{device="ixl1"} 0
node_network_receive_multicast_total{device="ixl2"} 9619
node_network_receive_multicast_total{device="ixl3"} 12926
node_network_receive_multicast_total{device="lagg0"} 0
node_network_receive_multicast_total{device="lagg1"} 22545
node_network_receive_multicast_total{device="lo0"} 0
node_network_receive_multicast_total{device="pflog0"} 0
node_network_receive_multicast_total{device="pfsync0"} 0
node_network_receive_multicast_total{device="vlan0.0002"} 0
node_network_receive_multicast_total{device="vlan0.0003"} 0
node_network_receive_multicast_total{device="vlan0.0060"} 269
node_network_receive_multicast_total{device="vlan0.0064"} 0
node_network_receive_multicast_total{device="vlan0.0068"} 767
node_network_receive_multicast_total{device="vlan0.0665"} 2260
node_network_receive_multicast_total{device="vlan0.0666"} 500
node_network_receive_multicast_total{device="vlan0.4087"} 0
node_network_receive_multicast_total{device="vlan0.4088"} 162
node_network_receive_multicast_total{device="vlan0.4089"} 0
node_network_receive_multicast_total{device="vlan0.4090"} 0
node_network_receive_multicast_total{device="vlan0.4091"} 0
node_network_receive_multicast_total{device="vlan0.4092"} 0
node_network_receive_multicast_total{device="vlan0.4093"} 0
node_network_receive_multicast_total{device="vlan0.4094"} 0
node_network_receive_multicast_total{device="wg1"} 0
# HELP node_network_receive_packets_total Network device statistic receive_packets.
# TYPE node_network_receive_packets_total counter
node_network_receive_packets_total{device="bge0"} 387
node_network_receive_packets_total{device="bge1"} 1.587478e+06
node_network_receive_packets_total{device="enc0"} 0
node_network_receive_packets_total{device="ixl0"} 0
node_network_receive_packets_total{device="ixl1"} 0
node_network_receive_packets_total{device="ixl2"} 1.958738e+06
node_network_receive_packets_total{device="ixl3"} 521306
node_network_receive_packets_total{device="lagg0"} 0
node_network_receive_packets_total{device="lagg1"} 2.480044e+06
node_network_receive_packets_total{device="lo0"} 484260
node_network_receive_packets_total{device="pflog0"} 0
node_network_receive_packets_total{device="pfsync0"} 0
node_network_receive_packets_total{device="vlan0.0002"} 0
node_network_receive_packets_total{device="vlan0.0003"} 0
node_network_receive_packets_total{device="vlan0.0060"} 1.002267e+06
node_network_receive_packets_total{device="vlan0.0064"} 0
node_network_receive_packets_total{device="vlan0.0068"} 7538
node_network_receive_packets_total{device="vlan0.0665"} 7376
node_network_receive_packets_total{device="vlan0.0666"} 1.339788e+06
node_network_receive_packets_total{device="vlan0.4087"} 0
node_network_receive_packets_total{device="vlan0.4088"} 54714
node_network_receive_packets_total{device="vlan0.4089"} 0
node_network_receive_packets_total{device="vlan0.4090"} 0
node_network_receive_packets_total{device="vlan0.4091"} 0
node_network_receive_packets_total{device="vlan0.4092"} 0
node_network_receive_packets_total{device="vlan0.4093"} 0
node_network_receive_packets_total{device="vlan0.4094"} 0
node_network_receive_packets_total{device="wg1"} 0
# HELP node_network_transmit_bytes_total Network device statistic transmit_bytes.
# TYPE node_network_transmit_bytes_total counter
node_network_transmit_bytes_total{device="bge0"} 8214
node_network_transmit_bytes_total{device="bge1"} 3.8848712e+07
node_network_transmit_bytes_total{device="enc0"} 0
node_network_transmit_bytes_total{device="ixl0"} 0
node_network_transmit_bytes_total{device="ixl1"} 0
node_network_transmit_bytes_total{device="ixl2"} 2.205393588e+09
node_network_transmit_bytes_total{device="ixl3"} 2.272282173e+09
node_network_transmit_bytes_total{device="lagg0"} 0
node_network_transmit_bytes_total{device="lagg1"} 4.477675761e+09
node_network_transmit_bytes_total{device="lo0"} 1.00744454e+08
node_network_transmit_bytes_total{device="pflog0"} 6.796487e+06
node_network_transmit_bytes_total{device="pfsync0"} 5.006733e+07
node_network_transmit_bytes_total{device="vlan0.0002"} 444110
node_network_transmit_bytes_total{device="vlan0.0003"} 443970
node_network_transmit_bytes_total{device="vlan0.0060"} 3.604166805e+09
node_network_transmit_bytes_total{device="vlan0.0064"} 443858
node_network_transmit_bytes_total{device="vlan0.0068"} 3.289052e+06
node_network_transmit_bytes_total{device="vlan0.0665"} 6.790559e+06
node_network_transmit_bytes_total{device="vlan0.0666"} 8.18647853e+08
node_network_transmit_bytes_total{device="vlan0.4087"} 444024
node_network_transmit_bytes_total{device="vlan0.4088"} 2.4188911e+07
node_network_transmit_bytes_total{device="vlan0.4089"} 443718
node_network_transmit_bytes_total{device="vlan0.4090"} 443858
node_network_transmit_bytes_total{device="vlan0.4091"} 443858
node_network_transmit_bytes_total{device="vlan0.4092"} 443788
node_network_transmit_bytes_total{device="vlan0.4093"} 443928
node_network_transmit_bytes_total{device="vlan0.4094"} 443578
node_network_transmit_bytes_total{device="wg1"} 0
# HELP node_network_transmit_drop_total Network device statistic transmit_drop.
# TYPE node_network_transmit_drop_total counter
node_network_transmit_drop_total{device="bge0"} 0
node_network_transmit_drop_total{device="bge1"} 0
node_network_transmit_drop_total{device="enc0"} 0
node_network_transmit_drop_total{device="ixl0"} 0
node_network_transmit_drop_total{device="ixl1"} 0
node_network_transmit_drop_total{device="ixl2"} 0
node_network_transmit_drop_total{device="ixl3"} 0
node_network_transmit_drop_total{device="lagg0"} 0
node_network_transmit_drop_total{device="lagg1"} 0
node_network_transmit_drop_total{device="lo0"} 0
node_network_transmit_drop_total{device="pflog0"} 0
node_network_transmit_drop_total{device="pfsync0"} 0
node_network_transmit_drop_total{device="vlan0.0002"} 0
node_network_transmit_drop_total{device="vlan0.0003"} 0
node_network_transmit_drop_total{device="vlan0.0060"} 0
node_network_transmit_drop_total{device="vlan0.0064"} 0
node_network_transmit_drop_total{device="vlan0.0068"} 0
node_network_transmit_drop_total{device="vlan0.0665"} 0
node_network_transmit_drop_total{device="vlan0.0666"} 0
node_network_transmit_drop_total{device="vlan0.4087"} 0
node_network_transmit_drop_total{device="vlan0.4088"} 0
node_network_transmit_drop_total{device="vlan0.4089"} 0
node_network_transmit_drop_total{device="vlan0.4090"} 0
node_network_transmit_drop_total{device="vlan0.4091"} 0
node_network_transmit_drop_total{device="vlan0.4092"} 0
node_network_transmit_drop_total{device="vlan0.4093"} 0
node_network_transmit_drop_total{device="vlan0.4094"} 0
node_network_transmit_drop_total{device="wg1"} 0
# HELP node_network_transmit_errs_total Network device statistic transmit_errs.
# TYPE node_network_transmit_errs_total counter
node_network_transmit_errs_total{device="bge0"} 0
node_network_transmit_errs_total{device="bge1"} 0
node_network_transmit_errs_total{device="enc0"} 0
node_network_transmit_errs_total{device="ixl0"} 0
node_network_transmit_errs_total{device="ixl1"} 0
node_network_transmit_errs_total{device="ixl2"} 0
node_network_transmit_errs_total{device="ixl3"} 0
node_network_transmit_errs_total{device="lagg0"} 7163
node_network_transmit_errs_total{device="lagg1"} 1.490613e+06
node_network_transmit_errs_total{device="lo0"} 0
node_network_transmit_errs_total{device="pflog0"} 0
node_network_transmit_errs_total{device="pfsync0"} 0
node_network_transmit_errs_total{device="vlan0.0002"} 21
node_network_transmit_errs_total{device="vlan0.0003"} 22
node_network_transmit_errs_total{device="vlan0.0060"} 1.490162e+06
node_network_transmit_errs_total{device="vlan0.0064"} 20
node_network_transmit_errs_total{device="vlan0.0068"} 109
node_network_transmit_errs_total{device="vlan0.0665"} 25
node_network_transmit_errs_total{device="vlan0.0666"} 53
node_network_transmit_errs_total{device="vlan0.4087"} 22
node_network_transmit_errs_total{device="vlan0.4088"} 36
node_network_transmit_errs_total{device="vlan0.4089"} 20
node_network_transmit_errs_total{device="vlan0.4090"} 20
node_network_transmit_errs_total{device="vlan0.4091"} 21
node_network_transmit_errs_total{device="vlan0.4092"} 20
node_network_transmit_errs_total{device="vlan0.4093"} 21
node_network_transmit_errs_total{device="vlan0.4094"} 21
node_network_transmit_errs_total{device="wg1"} 0
# HELP node_network_transmit_multicast_total Network device statistic transmit_multicast.
# TYPE node_network_transmit_multicast_total counter
node_network_transmit_multicast_total{device="bge0"} 0
node_network_transmit_multicast_total{device="bge1"} 6366
node_network_transmit_multicast_total{device="enc0"} 0
node_network_transmit_multicast_total{device="ixl0"} 0
node_network_transmit_multicast_total{device="ixl1"} 0
node_network_transmit_multicast_total{device="ixl2"} 62645
node_network_transmit_multicast_total{device="ixl3"} 64641
node_network_transmit_multicast_total{device="lagg0"} 0
node_network_transmit_multicast_total{device="lagg1"} 127286
node_network_transmit_multicast_total{device="lo0"} 0
node_network_transmit_multicast_total{device="pflog0"} 0
node_network_transmit_multicast_total{device="pfsync0"} 0
node_network_transmit_multicast_total{device="vlan0.0002"} 6351
node_network_transmit_multicast_total{device="vlan0.0003"} 6349
node_network_transmit_multicast_total{device="vlan0.0060"} 30989
node_network_transmit_multicast_total{device="vlan0.0064"} 6345
node_network_transmit_multicast_total{device="vlan0.0068"} 36856
node_network_transmit_multicast_total{device="vlan0.0665"} 6366
node_network_transmit_multicast_total{device="vlan0.0666"} 6364
node_network_transmit_multicast_total{device="vlan0.4087"} 6350
node_network_transmit_multicast_total{device="vlan0.4088"} 6352
node_network_transmit_multicast_total{device="vlan0.4089"} 6343
node_network_transmit_multicast_total{device="vlan0.4090"} 6345
node_network_transmit_multicast_total{device="vlan0.4091"} 6345
node_network_transmit_multicast_total{device="vlan0.4092"} 6344
node_network_transmit_multicast_total{device="vlan0.4093"} 6346
node_network_transmit_multicast_total{device="vlan0.4094"} 6341
node_network_transmit_multicast_total{device="wg1"} 0
# HELP node_network_transmit_packets_total Network device statistic transmit_packets.
# TYPE node_network_transmit_packets_total counter
node_network_transmit_packets_total{device="bge0"} 127
node_network_transmit_packets_total{device="bge1"} 426317
node_network_transmit_packets_total{device="enc0"} 0
node_network_transmit_packets_total{device="ixl0"} 0
node_network_transmit_packets_total{device="ixl1"} 0
node_network_transmit_packets_total{device="ixl2"} 1.864573e+06
node_network_transmit_packets_total{device="ixl3"} 2.053329e+06
node_network_transmit_packets_total{device="lagg0"} 0
node_network_transmit_packets_total{device="lagg1"} 3.917902e+06
node_network_transmit_packets_total{device="lo0"} 484260
node_network_transmit_packets_total{device="pflog0"} 87236
node_network_transmit_packets_total{device="pfsync0"} 160631
node_network_transmit_packets_total{device="vlan0.0002"} 6351
node_network_transmit_packets_total{device="vlan0.0003"} 6349
node_network_transmit_packets_total{device="vlan0.0060"} 2.793868e+06
node_network_transmit_packets_total{device="vlan0.0064"} 6345
node_network_transmit_packets_total{device="vlan0.0068"} 42317
node_network_transmit_packets_total{device="vlan0.0665"} 13894
node_network_transmit_packets_total{device="vlan0.0666"} 953208
node_network_transmit_packets_total{device="vlan0.4087"} 6350
node_network_transmit_packets_total{device="vlan0.4088"} 44288
node_network_transmit_packets_total{device="vlan0.4089"} 6343
node_network_transmit_packets_total{device="vlan0.4090"} 6345
node_network_transmit_packets_total{device="vlan0.4091"} 6345
node_network_transmit_packets_total{device="vlan0.4092"} 6344
node_network_transmit_packets_total{device="vlan0.4093"} 6346
node_network_transmit_packets_total{device="vlan0.4094"} 6341
node_network_transmit_packets_total{device="wg1"} 0
node_scrape_collector_success{collector="netdev"} 1
```
