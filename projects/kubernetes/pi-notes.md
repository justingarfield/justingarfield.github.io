# Raspberry Pi notes

## What Pi Model / Revision / SoC do I have?

* Turn on Pi Connected to HDMI monitor
* On initial boot screen, look for a code in the upper-left, like `d03114`
* Visit https://oastic.com/posts/how-to-know-which-raspberry-do-you-have/
* Search for your code

### My current Pi Model / Revision / SoCs

| Code | Model | Revision | SoC | RAM | Manufacturer |
|-|-|-|-|-|-|
| d03114 | 4B | 1.4 | BCM2711 | 8GB | Sony UK |

## Netboot

| Filename               | blksize | tsize   | Exists? |
|-|-|-|-|
| `/<serial>/start4.elf` | 1468    | 0       | No  |
| `/<serial>/start.elf`  | 1468    | 0       | No  |
| `/config.txt`          | 512     | 230     | Yes |
| `/pieeprom.sig`        | 1468    | 0       | No  |
| `/recover4.elf`        | 1468    | 0       | No  |
| `/recovery.elf`        | 1468    | 0       | No  |
| `/start4.elf`          | 512     | 2250784 | Yes |
| `/fixup4.dat`          | 512     | 5398    | Yes |

## Reference

* [Pi 4 - Boot EEPROM](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-eeprom)
* [Pi 4 - Boot Flow](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-4-boot-flow)
* [Raspberry Pi - The boot Folder](https://www.raspberrypi.com/documentation/computers/configuration.html#the-boot-folder)
