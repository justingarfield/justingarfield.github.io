# UCTronics LCD Screen

## Prerequisties

* You will need to have `git` installed and available, so that you can clone a repository

## Process

Here are the instructions as-per the [UCTronics Installation Manual](https://m.media-amazon.com/images/I/B1uwMAWN7HL.pdf)...

* Add the following to `/boot/config.txt`: `dtparam=i2c_arm=on,i2c_arm_baudrate=400000`
* Add the following to `/boot/config.txt`: `dtoverlay=gpio-shutdown,gpio_pin=4,active_low=1,gpio_pull=up`
* Clone the code repository, build and test software:
```bash
git clone https://github.com/UCTRONICS/SKU_RM0004.git
cd SKU_RM0004
make
./display 
```
* Add automatic Startup Script to `/etc/rc.local` (Add command to the `fi` of the `rc.local` file):
```bash
sudo nano /etc/rc.local

# Script to add here
cd /home/pi/SKU_RM0004
make clean
make
./display &
```

## Breaking it Down

I spend more of my time in software than low-level hardware, so I'm not always familiar with hardware-level device drives, flags, etc. Here we break-down what some of the lower-level stuff above means.

* [Device Trees, Overlays, and Parameters](https://www.raspberrypi.com/documentation/computers/configuration.html#device-trees-overlays-and-parameters)
* 

`dtparam=i2c_arm=on,i2c_arm_baudrate=400000`

* What does `dtparam` do?
  * 

* What is `i2c_arm=on` doing?

This is telling the Pi to enable the [Inter-Integrated Circuit (I2C)](https://learn.sparkfun.com/tutorials/i2c) optional hardware interface (CPU-side, not GPU-side)

Our **Controller** will be the Raspberry Pi, and the **Peripherals** will be the Power Button and LCD Screen.

* What is `i2c_arm_baudrate=400000` doing?

This is setting the communications speed in bits-per-second for I2C to **400,000bps** / **400kbps**.
