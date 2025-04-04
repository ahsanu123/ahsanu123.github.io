---
date: 2025-04-04
title: ⛅ Android Development Setup Without Android Studio in Kotlin Multiplatform
tags: linux, setup, android, kotlin, kotlin multiplatform
---

# ⛅ Android Development Setup Without Android Studio in Kotlin Multiplatform

![banner](https://github.com/user-attachments/assets/137052ba-02ef-4c2d-aa33-28e50acaffa0)

- ok first you need android cmdline tools [link](https://developer.android.com/tools), but mostly i use arch linux, and aur have this in [link](https://aur.archlinux.org/packages/android-sdk-cmdline-tools-latest), so run ` yay -S android-sdk-cmdline-tools-latest --noconfirm`
- but if you not in linux environment you need download cmdline-tools and extract it somewhere, make sure to export cmdline-tools folder to your environment 
>  Set environment variables [quote](https://developer.android.com/tools#environment-variables)
> We recommend setting the environment variable for  [`ANDROID_HOME`](https://developer.android.com/studio/command-line/variables#envar)  when using the command line. Also, set your command search path to include  `ANDROID_HOME/tools`,  `ANDROID_HOME/tools/bin`, and  `ANDROID_HOME/platform-tools`  to find the most common tools. The steps vary depending on your OS, but read  [How to set environment variables](https://developer.android.com/studio/command-line/variables#set)  for general guidance.
- after finish install of cmd-line tools add its path to your environment with `export PATH="$PATH:/opt/android-sdk/cmdline-tools/latest/bin"` 
- set your android sdk root with `export ANDROID_HOME=/opt/android-sdk`
- now you get 
```shell
ls /opt/android-sdk/cmdline-tools/latest/bin
apkanalyzer  d8    profgen  resourceshrinker  screenshot2
avdmanager   lint  r8       retrace           sdkmanager
```
- first we will use `sdkmanager` to update our sdk with `sdkmanager --update`
- next install minimal development  tool with (choose version based on your need, use `sdkmanager --list` to get all available package and its version)
```shell
sdkmanager "platform-tools" \
           "build-tools;34.0.0" \
           "platforms;android-34" \
           "system-images;android-34;google_apis;x86_64"
```
- when you run previous command you will ask about license, just type `y`, and make sure you have permission to your `ANDROID_HOME` , if not add permision with 
```shell
sudo chown -R $USER:$USER $ANDROID_HOME
sudo chmod -R 775 $ANDROID_HOME
```
- you can list installed package in `/opt/android-sdk`
- dont forget to add installed package to your path 
```shell
export PATH=$ANDROID_HOME/cmdline-tools/latest/bin:$PATH
export PATH=$ANDROID_HOME/platform-tools:$PATH
export PATH=$ANDROID_HOME/build-tools/34.0.0:$PATH
```
- finally install gradle, `sudo pacman -S gradle --noconfirm`
- you can create profile of all exported path with (note: I use ZSH)
```shell
// add this to your shell profile (mine is .zshrc)
export ANDROID_HOME=/opt/android-sdk
export PATH=$ANDROID_HOME/build-tools/34.0.0/:$PATH
export PATH=$ANDROID_HOME/cmdline-tools/latest/bin/:$PATH
export PATH=$ANDROID_HOME/emulator/:$PATH
export PATH=$ANDROID_HOME/platform-tools/:$PATH
```
### Create Android Project with gradle 
to create new application we can use kotlin multiplatform wizard
- we will use kotlin multiplatform wizard, [link](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html#create-the-project-with-a-wizard)
- choose your app name and click download 
- unzip to your project root folder
- next run `./gradlew` to download all needed dependencies
- ok, we will test our project by build debug app then run it on real android device 
- first we need to enable developer mode of our android device (step to activate android phone dev mode is different, so search for your phone)
- in android developer mode, choose `wireless debugging` and enable it
- make sure your android device is connected to same network with your computer
- in wireless debugging you will find your ip adress and your port
- we will use `adb` to connect into your phone with pair code, click on `pair deevice with pairing code` on your phone, you will give by needed information,
- run `adb pair <ip:port> <pair-code>` ex: `adb pair 192.168.1.10:41245 316836`
```shell
// example of successfull pair 
$ adb pair 192.168.1.10:43423 218265
Successfully paired to 192.168.1.10:43423 [guid=adb-1027525355008516-aqgzTQ]
```
- after pairing, you need to connect into device with `adb connect <ip:port>`, with this we can automatically install our app to connected android devices.
```shell
// example of successfull connect
$ adb connect 192.168.1.10:34771
connected to 192.168.1.10:34771
```
- next list your device with `adb devices`
```shell
$ adb devices
List of devices attached
192.168.1.10:34771      device
```
- you can list all gradlew task with `./gradlew tasks` 
- to build and install debug app to your phone run `./gradlew assembleDebug` 
- then install it with `./gradlew installDebug`
- if previous command success you will find new app in your phone (name will same as your project name)
- there also continuous mode, to re install if some file was changed `./gradlew installDebug --continuous`
