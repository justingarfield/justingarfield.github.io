+++
date = "2017-04-26T08:03:34+05:00"
draft = false
title = "Simple Arch Linux Virtual Machine Install"
tags = [ "arch", "linux" ]
categories = [ "Virtualization", "Operating Systems" ]
image = "simple-arch-linux-virtual-machine-install.png"
+++

# Simple Arch Linux Virtual Machine Install

## Overview

This article shares my experience with installing a very simple, bare-bones, Arch Linux instance inside a Virtual Machine (VM) environment. A lot of users seem to find the Arch Linux installation guides confusing and overwhelming, so I'm going to try and do a step-by-step install guide to get people up and running as best I know how at this point in time.

I highly recommend glancing through the [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide) before proceeding, so that you can easily catch any part of the process that may have changed since this guide was written.


## This article does _not_ cover

* Swap Partitions
* Partition Alignment
* What ESP is
* Secure Boot


## Prerequisites

This article assumes that you have the following:

* A Hypervisor installed and ready to go (e.g. [Oracle VirtualBox ](https://www.virtualbox.org/) or [Microsoft Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v))
* A VM with internet access ready to have Arch Linux installed
* An understanding of what Arch Linux is
* Experience using a Command-Line Interface

## Boot into the Live image

Before we can do anything, you need to make sure that you've [downloaded the Arch Linux ISO file](https://www.archlinux.org/download/) and created a new VM to run inside.

Mount the ISO file as a bootable medium (usually as a CD/DVD device) and tell your VM to boot from that.

Once booted into the live image, you should be logged in as root inside a Zsh shell.

## Test network connectivity

Arch should provide DHCP and acquire a network connection out-of-the-box, but sometimes a misconfigurted VM will prevent it from accessing the Internet.

To figure out if you have Internet connectivity, run the following command:

``` zsh
ping www.google.com
```

If you get replies, then you're good to go, if not, refer to the [Arch documentation for Networking](https://wiki.archlinux.org/index.php/Network_configuration).

## Sync to NTP Time Source

Now we want to tell Arch to synchronize its system clock with an NTP server to make sure our clock isn't off from the rest of the world. This helps down the road when doing things like collating logs, troubleshooting errors, and dealing with authentication TTLs and drift issues.

Run the following commands to enable NTP synchronization and also check the status afterward to ensure it got turned on:

``` zsh
timedatectl set-ntp true
timedatectl status
```

## BIOS vs. UEFI Installation

Before proceeding any further, you need to determine if you're installing on a machine with either a Basic Input/Output System (BIOS) or an implementation of the Unified Extensible Firmware Interface (UEFI). These are the sub-systems that are responsible for loading your boot-loader (GRUB) and launching into the actual operating system. More modern machines and hypervhisors support UEFI, but there's still plenty of hardware out there that uses BIOS, so I think it's important to cover them both.

To determine the type of installation you can pick, run the following command:

``` zsh
ls /sys/firmware/efi/efivars
```

### Is it BIOS?

If you have a BIOS-based machine, then you should get an error stating _ls: cannot access '/sys/firmware/efi/efivars': No such file or directory_

### Is it UEFI?

If you have a UEFI-based machine, then you should see a bunch of output with entries starting with words like Boot, BootCurrent, ErrOut, LoaderInfo, and others.


## Partition the Disk

Since we're going for simplicity in this article, we'll focus on a single hard drive (or block device if you prefer). The following commands will:

_Note: I am not worried about a swap partition in this example. If you need a swap partition, you would simply created a 3rd partition, and then run a set command to make sure it's of type swap._

{{< tabs "partition-disk" >}}
    {{< tabs-title title="UEFI" isActive="true" >}}
    {{< tabs-title title="BIOS" >}}
{{< /tabs >}}

{{< tabs-content "partition-disk" >}}

    {{% tabs-panel title="UEFI" isActive="true" %}}

* Create a new GPT-type partition table
* Create a partition of 550MiB to hold the EFI System Partition (ESP)
* Create a partition for the Arch Linux root file-system that conusmes 100% of the rest of the drive
* Set the ESP partition flag on the ESP partition
* Name both the Boot and Root partitions to make them easier to identify

``` zsh
parted /dev/sda mklabel gpt
parted /dev/sda mkpart primary fat32 1 579
parted /dev/sda mkpart primary ext4 579 100%
parted /dev/sda set 1 esp on
parted /dev/sda name 1 boot
parted /dev/sda name 2 root
```

    {{% /tabs-panel %}}

    {{% tabs-panel title="BIOS" %}}

* Create a new GPT-type partition table
* Create a partition of 3MB to hold the GRUB core.img (used in BIOS/GPT-style configuration)
* Create a partition of 256MB for the GRUB bootloader files
* Create a partition for the Arch linux root file-system that consumes 100% of the rest of the drive
* Set the _bios\_grub_ flag on partition 1
* Set the _boot_ flag on partition 2
* Name both the partitions accordingly to make them easier to identify

``` zsh
parted /dev/sda mklabel gpt
parted /dev/sda mkpart primary ext2 1 3
parted /dev/sda mkpart primary ext2 3 259
parted /dev/sda mkpart primary ext4 259 100%

parted /dev/sda set 1 bios_grub on
parted /dev/sda set 2 boot on
parted /dev/sda name 1 grub
parted /dev/sda name 2 boot
parted /dev/sda name 3 root
```

    {{% /tabs-panel %}}

{{< /tabs-content >}}


## Create and Mount File-Systems

Now that we have our disk partitioned to our needs, it's time to create the file-systems on top of them and mount them as part of the root file-system. The following commands will:

{{< tabs "create-and-mount" >}}
    {{< tabs-title title="UEFI" isActive="true" >}}
    {{< tabs-title title="BIOS" >}}
{{< /tabs >}}

{{< tabs-content "create-and-mount" >}}

    {{% tabs-panel title="UEFI" isActive="true" %}}

* Create a FAT32 file-system on /dev/sda1
* Create an EXT4 file-system on /dev/sda2
* Mount the EXT4 file-system on /dev/sda2 as /mnt
* Create a directory named /mnt/boot/efi
* Mount the FAT32 file-system on /dev/sda1 to /mnt/boot/efi

``` zsh
mkfs.fat -F 32 /dev/sda1
mkfs.ext4 /dev/sda2

mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

    {{% /tabs-panel %}}

    {{% tabs-panel title="BIOS" %}}

* Create an EXT2 file-system on /dev/sda1 and /dev/sda2
* Create an EXT4 file-system on /dev/sda3
* Mount the EXT4 file-system on /dev/sda3 as /mnt
* Create a directory named /mnt/boot
* Mount the EXT2 file-system on /dev/sda2 to /mnt/boot

``` zsh
mkfs.ext2 /dev/sda2
mkfs.ext4 /dev/sda3

mount /dev/sda3 /mnt
mkdir -p /mnt/boot
mount /dev/sda2 /mnt/boot
```
        
    {{% /tabs-panel %}}

{{< /tabs-content >}}


## Rank Mirror Sites

Before we can download and install the base package for Arch Linux, we should really pull down the current list of mirror sites and rank them for the fastest available downloads. The following commands will:

* Use _curl_ to download an up-to-date list of the Arch Linux Mirror Sites and save them to a file named _mirrorlist_
* Uncomment all of the mirrors listed in the new mirrorlist file
* Use the _rankmirrors_ program to contact all of the mirror sites, rank them according to speed, and then save a newly sorted listed of mirrors 

``` zsh
curl -o mirrorlist "https://www.archlinux.org/mirrorlist/?country=US&protocol=http&ip_version=4"
sed `/^#\S/ s|#||` -i mirrorlist
/usr/bin/rankmirrors -n 0 mirrorlist > /etc/pacman.d/mirrorlist
```


## Install the base

We are now ready to download and install the base package of Arch Linux. This may take a while depending on your hardware and connection speed.

``` zsh
pacstrap /mnt base
```


## Generate new fstab file

We want to make sure that we persist the state of what file-systems are mounted, where, and with what settings. To do this we use the genfstab program like so:

``` zsh
genfstab -U -p /mnt >> /mnt/etc/fstab
```


## Change the root file-system

Next we change the root file-system to work on the new system install.

``` zsh
arch-chroot /mnt
```


## Configuring the Timezone to UTC

Keep in mind that timedatectl will not work inside of the chroot environment. That is why we must configure things this way. Set the Hardware Clock from the System Clock, and update the timestamps in /etc/adjtime using the UTC timescale.
_Note: This is all that timedatectl is doing in the end anyway, this is just manual._

``` zsh
ln -sf /usr/share/zoneinfo/UTC /etc/localtime
hwclock --systohc --utc
```


## Setup our locale for en_US / UTF-8

``` zsh
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```


## Configure the /etc/hostname and /etc/hosts files for networking

_Note: Hostname can only be a maximum of 64-characters, per Linux limitation_

``` zsh
echo examplehost > /etc/hostname
echo "127.0.0.1 examplehost.example.tld examplehost" >> /etc/hosts
```

## Enable Microcode Updates for Intel-based machines

If you're on an Intel-based chipset, it is HIGHLY recommended that you install the intel-ucode package. This allows optimizations and bug-fixes to be applied to the CPU by the Linux Kernel, so that you're ensured stability and performance enhancements that have been applied to the CPU over time.

By installing this package at this point in time, GRUB will be able to auto-detect and tell the system to load this automatically vs. you having to manually copy files and edit configs yourself.

``` zsh
pacman -S intel-ucode
```

_Note: If you're on an AMD chipset, you are already covered by the base package loading the linux-firmware module._


# Bootloader Installataion

{{< tabs "bootloader-installation" >}}
    {{< tabs-title title="UEFI" isActive="true" >}}
    {{< tabs-title title="BIOS" >}}
{{< /tabs >}}

{{< tabs-content "bootloader-installation" >}}

    {{% tabs-panel title="UEFI" isActive="true" %}}

``` zsh
pacman -S grub efibootmgr
grub-install --no-floppy --recheck
```

    {{% /tabs-panel %}}

    {{% tabs-panel title="BIOS" %}}
        
``` zsh
pacman -S grub
grub-install --no-floppy --recheck --target=i386-pc /dev/sda
```
        
    {{% /tabs-panel %}}

{{< /tabs-content >}}


## Grub .mo file

These commands will ensure that the proper locale-related grub files exist at /boot/grub/locale, so that it can properly function when booting.

According to the Arch forums, this was fixed and handled by GRUB itself now, but I've had issues where this is not the case, so I recommend doing these steps regardless.

``` zsh
mkdir -p /boot/grub/locale
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
grub-mkconfig -o /boot/grub/grub.cfg
```


## Pick a root password

``` zsh
passwd
```


## Add User Account

``` zsh
useradd -m -G wheel someone
passwd someone
```


## Cleanup and Final Steps

You're now ready to cleanup and reboot your newly installed Arch Linux install.

* Exit the chroot jail, back to the live image running in memory
* Un-mount the mountpoints we configured
* Shutdown the machine
* Remove virtual media

``` zsh
exit
umount -R /mnt
shutdown now
```

* Remove the bootable media for the live image, now that there's a bootloader and OS installed on the drive.
* Start your machine back up and enjoy!


## Acquiring an IP Address

``` zsh
systemctl enable dhcpcd.service
systemctl start dhcpcd.service
```


## Resources

* https://wiki.archlinux.org/index.php/installation_guide
* https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface
* https://askubuntu.com/questions/53578/can-i-install-in-uefi-mode-with-the-alternate-installer/57380#57380
* http://www.rodsbooks.com/efi-bootloaders/principles.html
* https://wiki.archlinux.org/index.php/Change_root
* [man parted](https://linux.die.net/man/8/parted  )
* [info parted](http://info2html.sourceforge.net/cgi-bin/info2html-demo/info2html?(parted.info.gz)Command%2520explanations)



## More Stuff

useradd -m jimbob

pacman -S sudo

/etc/sudoers - sudo configuration file
visudo - use this for edits...proeprly locks and checks before swapping out

``` zsh
visudo -f /etc/sudoers.d/001_configure_editor_defaults

Defaults      env_reset
Defaults      editor=/usr/bin/nano, !env_editor
```

* echo "Defaults env_reset" > /etc/sudoers.d/001_configure_editor_defaults
* echo "Defaults editor=/usr/bin/nano, !env_editor" >> /etc/sudoers.d/001_configure_editor_defaults

* echo "%wheel ALL=(ALL) ALL" > /etc/sudoers.d/002_allow_wheel_any_command

* echo "Defaults requiretty" > /etc/sudoers.d/003_require_tty_prevent_clearpass

``` zsh
sudo passwd -l root
```

# To unlock root account if needed

``` zsh
sudo passwd -u root
```


/etc/dhcpcd.conf
interface eth0
static ip_address=192.168.0.10/24	
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8

