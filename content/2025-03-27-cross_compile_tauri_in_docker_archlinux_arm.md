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
- next we will install required package to cross compile `sudo  pacman  -S  --needed   webkit2gtk-4.1   base-devel  curl   wget   file   openssl   appmenu-gtk-module   libappindicator-gtk3  librsvg yarn`
- install rust and rustup `pacman -S rustup`
- then install default toolchain for current architecture with `rustup default stable`
- we need node js to compile our frontend, `pacman -S nodejs`
- you need postgresql libs if you work with postgresql in rust, `pacman -S postgresql-libs`
- to save docker run -> docker ps && docker commit <container_id_or_name> my-custom-image
- to copy from docker container use ` docker cp tauri-container:/root/ams/app/src-tauri/target/release/bundle/deb/ams_0.1.0_armel.deb .`
- to run docker with name use ` docker run -it --rm --name tauri-container archlinuxarm:latest  /bin/bash`

