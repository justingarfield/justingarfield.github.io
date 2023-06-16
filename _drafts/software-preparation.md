# Part 3 - Software Preparation

This is part of a multi-part series:

* [Part 1 - Introduction](../README.md)
* [Part 2 - Hardware Preparation](hardware-preparation.md)
* [Part 3 - Software Preparation](software-preparation.md) <-- You are here
* [Part 4 - Sidero Metal](sidero-metal.md)
* [Part 5 - Talos OS]()
* [Part 6 - Provision a non Pi node]()

The current [Sidero on Raspberry Pi 4](https://www.sidero.dev/v0.5/guides/sidero-on-rpi4/) guide will point you to [Documentation -> Talos Linux Guides -> Installation -> Single Board Computers -> Raspberry Pi 4 Model B](https://www.talos.dev/v1.3/talos-guides/install/single-board-computers/rpi_4/), but this page is now deprecated, and you'll want to navigate to [Documentation -> Talos Linux Guides -> Installation -> Single Board Computers -> Raspberry Pi Series](https://www.talos.dev/v1.3/talos-guides/install/single-board-computers/rpi_generic/) instead.

## Flash the Bootloader on your Pis

Before doing anything with the Pis themselves, we're going to create an SD Card to flash all of them with an up-to-date bootloader that boots from the SD Card, followed by USB-device.

TL;DR

* Install the [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
* Insert an SD Card to flash
* Click **Choose OS** -> **Misc utility images** -> **Bootloader** -> **SD Card Boot**
* Click **Choose Storage** -> Pick your SD Card to flash the bootloader files onto
* **Double-check** that you picked the right SD Card (_you're going to erase the entire thing!!_)
* Click **Write**
* Remove the SD card from your local machine and insert it into the Raspberry Pi
* Power the Raspberry Pi on, and wait at least 10 seconds
* If successful, the green LED light will blink rapidly (forever), otherwise an error pattern will be displayed
  * Note: If an HDMI display is attached to the port closest to the power/USB-C port, the screen will display green for success or red if a failure occurs
* Power off the Raspberry Pi and remove the SD card from it

Repeat the above steps for each Pi.

## Prepare your Talos OS SD Card for installations

* Download the latest `metal-rpi_generic-arm64.img.xz` file found at [https://github.com/siderolabs/talos/releases/](https://github.com/siderolabs/talos/releases/)
* Insert an SD Card to flash
* Click **Choose OS** -> **Use custom** -> pick your downloaded `metal-rpi_generic-arm64.img.xz` file
* Click **Choose Storage** -> Pick your SD Card to flash Talos OS files onto
* **Double-check** that you picked the right SD Card (_you're going to erase the entire thing!!_)
* Click **Write**
