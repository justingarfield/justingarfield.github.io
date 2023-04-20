---
layout: base
title: Sophos Firewall
---

# Notes: Archive: Sophos Firewall

## When installing
* Connect a Keyboard and Monitor, you may have to do some CLI-level stuff on the firewall itself before the remote Web UI works
* If you can, connect the WAN and LAN cables to the firewall, so that it will auto-detect and auto-setup the Interfaces for you during install.

## Change default admin password (if not done already)
1. Navigate to Administration
2. Click on Device access tab
3. Find the "Default admin password settings" section
4. Change password and click Apply button

## Verify time settings
1. Navigate to Administration
2. Click on Time tab
3. Verify if time and other settings are correct

## Firmware Updates
1. Navigate to Backup & firmware
2. Click on Firmware tab
3. Click on Check for new firmware button
4. Install any new firmware if found

## Pattern Updates
1. Navigate to Backup & firmware
2. Click on Pattern updates tab
3. Click on Update pattern now button
4. Install any new patterns if found

## Add Named MAC Addresses
1. Navigate to Hosts and services
2. Click on MAC host
3. Add records for each MAC address you know of (choose a Type of "MAC list" if entering multiple MAC addresses for a single device)

## Setup Shedules, Access times, Surfing Quotas, etc.
1. Navigate to Profiles
2. Click on Schedule, Access time, or Surfing quota tab
3. Setup profiles for whatever sort of rules you want

## BACKUP YOUR CONFIGURATION
1. Navigate to Backup & firmware
2. Click on Backup & restore tab
3. Find the Backup section
4. Pick your settings and download a backup of the config

Note: Always do this before applying future Firmware upgrades, or before/after you decide to make giant ruleset changes.

## Trunk Port Mode (Multi VLAN Support)
If you want to be able to have multiple VLANs going, and be able to do Inter-VLAN routing of traffic, you will need to make sure that the "Access Mode" of the Network Switch-port that the LAN interface is connected to is set to "Trunk". Trunk'ed ports allow multiple VLAN tags to be passed. Once the Switch-port is in Trunk mode, you can then add multiple VLAN Interfaces to the LAN port in Sophos, and it can then work with traffic on mutliple VLANs.
