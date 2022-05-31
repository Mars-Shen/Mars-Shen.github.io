---
published: false
layout: post
date: 2022-07-27T12:00:00.000Z
author: Mars Shen
header-img: img/Header/coder-doom-dos-code-hd.jpg
catalog: true
tags:
  - 原创
  - switch硬破教程
  - switch
---
## Switch 破解术语

- **RCM**

RCM 是 ReCovery Mode 的缩写, 即恢复模式.

- **Horizon OS (HOS)**

Horizon 是 Nintendo Switch 操作系统的名称.

- **OFW**

OFW 是 Original Firmware的缩写, 即官方原版胸固件.

这指的是你设备中没有被修改的原始系统固件.

- **CFW**

CFW 是 Custom Firmware 的缩写, 即自制系统固件.

这指的是被修改的自制系统固件, 第三方自制程序(homebrew)被允许在此类系统固件上运行.

- **Homebrew**

一般用于描述开发人员为锁定的(非开放的)系统创建的非官方软件.

对于 Switch 来说, 此类软件可以包括存档管理器, 模拟器或破解游戏.

- **Atmosphere**

一个免费开源的第三方自制固件(CFW), [Github地址](https://github.com/Atmosphere-NX/Atmosphere).

- **SXOS**

由 Team Xecuter 创建的付费并封闭源代码的自制固件(CFW), 由于TX团队已经由于破解盈利被抓捕, 所以相关固件也已经不再更新.

不适用于 11.0.0 以上的固件.

- **NAND**

NAND是switch设备中内置的存储(不要与 SD 卡存储混淆).

Switch 一般拥有 32GB 内置存储(25.9GB 可用存储, 其余被系统占用).

SysNAND
SysNAND stands for "System NAND".
The SysNAND is the contents stored on your devices built in memory (not to be confused with OFW).
It contains everything ranging from the operating system itself, to games, screenshots, videos, and save files.
Every device, hacked or unhacked, has a SysNAND.

EmuNAND
EmuNAND stands for "Emulated NAND".
The EmuNAND is a copy of your SysNAND that resides on your SD card and not on your devices internal memory.
Think of it as having a secondary Nintendo Switch on your SD card.
It is used to isolate any actions that you might perform from your SysNAND that could either
harm your device or performing any other actions that might get you banned.
Any changes or actions that you perform in your EmuNAND won't affect your SysNAND since it is just a copy.

Having an EmuNAND on its own won't protect you from being banned. Click here for more information on how to avoid being banned.

Signature Patches
Signature patches or SigPatches allow your device to bypass signature checks performed by horizon
for installed titles.

Hekate
A custom bootloader that allows you to bypass fuse checks and has a lot of useful tools.
Can be used to boot into OFW, CFW, Android, Linux, and any other payload.

Fusee
A custom minimal bootloader that is only used to boot into Atmosphere.
The difference between Hekate and Fusee is that Hekate needs a custom configuration file that
tells Atmosphere what KIP's, Patches and other settings it should use, while Fusee boots them automatically.

NSP
Nintendo Submission Package or NSP is either a dump of an installed title or a homebrew package that can be installed.

XCI
NX Card Image or XCI is a dump from a Nintendo Switch cartridge.

NSZ
A compressed NSP file.

XCZ
A compressed XCI file.

NRO
An extension that homebrew applications use.


