---
layout: post
title: Build a custom Talos Linux Raspberry Pi Generic image with a custom System Extension
date: 2023-02-27 00:00:00 -0500
categories: Linux, Kubernetes, Containerization
tags: 
---
# Overview

If you're working with Raspberry Pis when installing Talos Linux, chances are you followed a [Talos SBC install guide](https://www.talos.dev/v1.3/talos-guides/install/single-board-computers/rpi_generic/) or a [Sidero SBC install guide](https://www.sidero.dev/v0.5/guides/sidero-on-rpi4/).

Chances are you'll probably have a custom power button, LCD display, infrared sensor, etc. that you need to run some code for on the Pi. In order to do this, we should use a System Extension to extend Talos.

If you noticed when setting up Talos on your Pi, you used a pre-baked raw image file to place onto your SD Card, which means that Talos is technically "already installed" since the process of using `dd` or the **Pi Imager** will expand the raw image as-is to your SD Card. This means that when Talos boots up, it will fully bypass the Installation process, and therefore ignore anything specified in the `machine.install.extensions` configuration section.

So how do we pre-bake our own System Extensions into the image that we expand onto the SD Card? Continue reading below to find out!

# Assumptions

* You already have a GitHub account
* You're using the GitHub CLI tooling
* You are on a Windows 11 Professional machine running Docker Desktop on-top of WSL w/ Ubuntu 22.04
  * Note: These steps will most likely still work on full Debian/Ubuntu Linux, but may not behave 100% the same _(e.g. errors encountered)_

# The Existing Talos Docs

If you visit the official [Talos documentation for System Extensions](https://www.talos.dev/v1.3/talos-guides/configuration/system-extensions), you'll find a section titled [Building a Talos Image with System Extensions](https://www.talos.dev/v1.3/talos-guides/configuration/system-extensions/#building-a-talos-image-with-system-extensions).

If you're as new as I am to Talos, you're probably staring at `make image-metal IMAGER_SYSTEM_EXTENSIONS="ghcr.io/siderolabs/amd-ucode:20220411 ghcr.io/siderolabs/gvisor:20220405.0-v1.0.0-10-g82b41ad"` and wondering where to go from here.

# Acquire and Build imager in the Talos Repository

First we need to clone the talos repository locally to our machine.

Assuming you're using Ubuntu under WSL on Windows, navigate to your bash shell and clone the official Talos repository.
```bash
mkdir -p ~/src/siderolabs

gh repo clone siderolabs/talos ~/src/siderolabs/talos
cd ~/src/siderolabs/talos

make imager
```

At this point, if you received an error, please see the [Troubleshooting](#troubleshooting) section at the bottom of this post.

If you didn't receive any errors, and your output looks like this, then you should be ready to rock!
```
 => exporting to image
 => => exporting layers
 => => writing image sha256:387e6d4e54dfaaf0107794614650329beb3a3da2fcf2f75c2664500ea3983d01
 => => naming to ghcr.io/siderolabs/imager:v1.4.0-alpha.1-43-g5a01d5fd4
make[2]: Leaving directory '/home/username/src/siderolabs/talos'
make[1]: Leaving directory '/home/username/src/siderolabs/talos'
```

If you want to see what the output inside of the image is, you can do the following to see it all in `/_imager_temp`:
```bash
DEST=_imager_temp make imager
```

# Push up your own build of the Talos imager
```
TAG=v1.4.0-jpgtest.1 \
  USERNAME=justingarfield \
  PUSH=true \
  make imager
```

# Mixin the System Extension(s)
```
  TAG=v1.4.0-jpgtest.1 \
  USERNAME=justingarfield \
  IMAGER_SYSTEM_EXTENSIONS="ghcr.io/justingarfield/sku_rm0004:0.1.15" \
  make sbc-rpi_generic
```

# Troubleshooting

## `ERROR: failed to solve: granting entitlement security. insecure is not allowed by build daemon configuration`

If you're receiving this error for any of the Makefile targets, it's because the `--allow security.insecure` flag being passed to `docker buildx build` won't be honored. When you launch Docker Desktop, it's going to spawn its own copy of `buildkitd`, which is what `docker buildx build` will dispatch new builds to from WSL. Since Docker Desktop controls this default instance of `buildkitd`, you will need to configured it at the Docker Desktop level itself.

* Open up **Docker Desktop UI** -> **Settings** -> **Docker Engine**
* Add the following `Entitlements` section to the configuration file (under the `builder` section):
  ```json
  {
    "builder": {
      "Entitlements": {
        "security-insecure": true
      }
    }
  }
  ```
* Restart Docker Desktop
  * Note: Docker Desktop locked-up on me when I attempted this, which required a full reboot of Windows to finalize

`buildkitd` should now have an _allowed extra privileged entitlement_ of `security.insecure` ([see here](https://github.com/docker/buildx/blob/v0.10.3/docs/reference/buildx_build.md#-allow-extra-privileged-entitlement---allow)) which _allows executions without a sandbox_ ([see here](https://docs.docker.com/engine/reference/builder/#run---securitysandbox)). 

