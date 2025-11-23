---
date: 2025-03-27
title: Cross Compile Tauri on Docker Archlinuxarm for Raspberry pi zero 2w 
tags: linux, howto, tauri
---

# 🍄 Cross Compile Tauri on Docker Archlinuxarm for Raspberry pi zero 2w 

> Its just quick note about setup and cross compile tauri for archlinuxarm architecture in docker 

first you need Docker installed on your system

```shell
$ docker --version
Docker version 28.0.1, build 068a01ea94  
```
- download Archlinuxarm image with `wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-armv7-latest.tar.gz`
- next create folder to extract it `mkdir archlinuxarm`
- extract to `archlinuxarm` by `tar -xpf ArchLinuxARM-rpi-armv7-latest.tar.gz -C archlinuxarm`
- then change directory with `cd archlinuxarm`
- now we will import extracted file to docker with `tar -cpf - . | docker import - archlinuxarm:latest`
- then run with, `docker run -it --rm archlinuxarm:latest /bin/bash `, it will run bash with interactive shell `--rm` will remove all data after you logout from shell
- if error when docker run, try to add qemu -> `docker run --rm --privileged multiarch/qemu-user-static --reset -p yes`
- init pacman key with `pacman-key --init`
- then `pacman-key --populate archlinuxarm`
- finally update all package with `pacman -Syu`
- next login as root and we will install required package to cross compile `pacman  -Syu  --needed  sudo webkit2gtk-4.1   base-devel  curl   wget   file   openssl   appmenu-gtk-module   libappindicator-gtk3  librsvg yarn git rustup nodejs unzip cargo-tauri gcc make binutils --noconfirm`
- then install default toolchain for current architecture with `rustup default stable`
- inside root user run this `echo "%wheel ALL=(ALL) ALL" >> /etc/sudoers`
- we need `arm-linux-gnueabihf-gcc` from aur, https://aur.archlinux.org/packages/arm-linux-gnueabihf-gcc
- next we need `yay` to install `arm-linux-gnueabihf-gcc`
- you can follow the instruction on yay [repo](https://github.com/Jguer/yay)
- if you dont have permission to automatically install, you can run `makepkg -s` to only build, then run `pacman -U --noconfirm yay-bin-*.pkg.tar.xz` to install using pacman.
- dont forget to run this after yay installation `yay -Y --gendb`
- you need postgresql libs if you work with postgresql in rust, `pacman -S postgresql-libs`
- next we can build our tauri application (more info here https://v2.tauri.app/distribute/)
- run `yarn tauri build` in application root folder, then wait
- it will generate executable and bundling to several bundle type,
- if you dont want bundle, just add flag `--no-bundle`
- to save docker run -> docker ps && docker commit <container_id_or_name> my-custom-image
- to copy from docker container use ` docker cp tauri-container:/root/ams/app/src-tauri/target/release/bundle/deb/ams_0.1.0_armel.deb .`
- to run docker with name use ` docker run -it --rm --name tauri-container archlinuxarm:latest  /bin/bash`

