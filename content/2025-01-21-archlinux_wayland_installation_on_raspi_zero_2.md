---
date: 2025-01-21
title: archlinux wayland installation on raspberry pi zero 2 
tags: linux, howto, installation
---
# 🐧 Introduction

[(skip intro if you want, its not important)](#installation-step)

I have a raspberry pi zero 2 w (let's call it zero 2 after this), 
but I never use it, recently I got a plan to run a local web app on my pi zero 2, 
so I try find lightest linux distro, I have several experience on archlinux with 
XWindow(xorg), so I try to install it on my zero 2, but sadly it doesn't 
work for unknown reason, I try to investigate it for a week, but no luck.

Then I try to install raspios lite, its work, with [wayfire](https://wayfire.org/) on [wayland](https://wayland.freedesktop.org/). although it work, the os is too slow to run browser. 

So I take another change to re-install archlinux with wayland, and luckly its worked,
and here is my documentation to install it. 

> ## ⚠️ Disclaimer
> I am not professional about linux, so use this text at your own risk.

---

# Installation Step

> 💿 Booting zero 2 with archlinuxarm os 

most of this part was copied from [official archlinuxarm installation guide](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-zero-2), and assume you work on linux for this installation.

- firstly prepare your sdcard, then insert with card reader into you pc
- next try to list your sdcard with `sudo ls /dev/sd*`, output command will look something like this 

```shell
$ ls /dev/sd*
/dev/sda  /dev/sdb  
```

- you will find several device, make sure you choose correct device **sdb** in this case 
- next replace all **X** with your device (**b** in this case)
- Start fdisk to partition the SD card 

```shell
fdisk /dev/sdX
```

- At the fdisk prompt, delete old partitions and create a new one:
  
  -  Type o. This will clear out any partitions on the drive.
  -  Type p to list partitions. There should be no partitions left.
  -  Type n, then p for primary, 1 for the first partition on the drive, press ENTER to accept the default first sector, then type +200M for the last sector.
  -  Type t, then c to set the first partition to type W95 FAT32 (LBA).
  -  Type n, then p for primary, 2 for the second partition on the drive, and then press ENTER twice to accept the default first and last sector.
  -  Write the partition table and exit by typing w.

- Create and mount the FAT filesystem:
```shell
mkfs.vfat /dev/sdX1
mkdir boot
mount /dev/sdX1 boot
```

- Create and mount the ext4 filesystem:
```shell
mkfs.ext4 /dev/sdX2
mkdir root
mount /dev/sdX2 root
```

- Download and extract the root filesystem (as root, not via sudo):
```shell
wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-armv7-latest.tar.gz
bsdtar -xpf ArchLinuxARM-rpi-armv7-latest.tar.gz -C root
sync
```

- Move boot files to the first partition:
```shell
mv root/boot/* boot
```

- Unmount the two partitions:
```shell
umount boot root
```
- Insert the SD card into the Raspberry Pi and apply 5V power.
- Use the serial console or attach HDMI and a keyboard
  - login as root with username `root` and password is `root`

- connect to network (internet)
- there is many way to connect into network, we will use `wpa_supplicant`
  - first generate config with `wpa_passphrase <your-wifi-name> <your-wifi-password> > homenetwork.conf`, ex: `wpa_passphrase homenetwork 1234567890 > homenetwork.conf`
  - then try to connect with `wpa_supplicant -i wlan0 -c homenetwork.conf`, `-i` is for interface (might be different, mine is **wlan0**), and `-c` is for config.
  - try to ping `ping 1.1.1.1`, make sure you got a response

- if `wpa_supplicant` and `wpa_passphrase` not working, try to use `netctl` by
  - set wlan0 (or your device down) -> `sudo ip link set wlan0 down`
  - then create/or copy `netctl` config by copying from `/etc/netctl/examples`, change SSID and password based on your need 
  - then run `netctl stop-all` to stop all services of netctl
  - finally run your config by `netctl start yourConfigName`, hopefully its connect 
  - now try to ping `ping 1.1.1.1`, make sure you got a response

- if you still cant connect to any password protected network, try to use mobile network (tethering), there is example inside `etc/netctl/example`, it always work for me
- Initialize the pacman keyring and populate the Arch Linux ARM package signing keys:
```shell
pacman-key --init
pacman-key --populate archlinuxarm
```

- next install several package to run wayland
  - `pacman -Syu base-devel sudo --noconfirm`, update and install base development package, sudo 
  - `pacman -S wayland xorg --needed --noconfirm` install wayland and xorg (at the time wayland is not complete yet, and most of app was based on xorg, so there still need xorg to work)
  - `pacman -S sway alacritty vim less --noconfirm` install _sway_ window manager (compositor), and alacritty for terminal emulator

- after installation complete, give default `alarm` user to sudo. 
  - run `visudo`, then uncomment `%wheel ALL=(ALL:ALL) ALL`, its about line 125
  - type `:wq` then enter, to save and exit

- next copy sway config into home `~/.config/sway/` directory, `cd ~ && cp /etc/sway/config ~/.config/sway`
- next change default terminal to use alacritty, `vim ~/.config/sway/config`
  - change `$term` variable to alacritty, from `set $term foot` to `set $term alacritty`
  - save and exit, type `:wq` then enter

- then re-login to user `alarm` 
- inside terminal with `alarm` user, run sway `sway`.

## Inside Sway 

by default you can run terminal by hit `mod + Enter` (mod is _windows_ key if you in windows).
you can read other shortcut key on `~/.config/sway/config`, to ease of reading config you can run `cat ~/.config/sway/config | less`


## Troubleshooting

if something error when you run `sway`, install below then try to restart by `sudo reboot now`.

- try to install _glib2_, `sudo pacman -S glib2 xf86-video-fbdev glu mesa-utils --noconfirm` 

## Reference 

- [official archlinuxarm installation guide](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-zero-2)

