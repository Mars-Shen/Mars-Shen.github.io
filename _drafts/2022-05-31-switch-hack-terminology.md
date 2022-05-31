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

- **SysNAND**

	SysNAND 是 "System NAND" 的缩写, 即设备机身内置存储.

	SysNAND中的内容是存储在机身内置存储中的内容(不要与OFW混淆).

	它包含从操作系统本身到游戏、屏幕截图、视频和保存文件的所有内容.

	无论是被破解或未被破解的设备都有一个SysNAND.

	我们常说的正版系统一般就存储在此.

- **EmuNAND**

	EmuNAND 是 "Emulated NAND"的缩写, 即虚拟内置存储.

	EmuNAND 是 SysNAND的副本，它存储在 SD 卡上，而不是在设备内部存储器上.

	可以把它想象成在你的 SD 卡上 有一个独立的额外的Nintendo Switch. 

	一般在 EmuNAND 中的任何更改或操作都不会影响您的 SysNAND，因为它是一个独立的副本。

	我们常说的虚拟系统一般就存储在此.
    
   ````
	注意: 使用 EmuNAND 并不能确保你不会被Ban. 单击此处了解有关如何避免被Ban的更多信息.
   ````
	

- **Signature Patches**

Signature patches or SigPatches allow your device to bypass signature checks performed by horizon
for installed titles.

- **Hekate**

A custom bootloader that allows you to bypass fuse checks and has a lot of useful tools.
Can be used to boot into OFW, CFW, Android, Linux, and any other payload.

- **Fusee**

A custom minimal bootloader that is only used to boot into Atmosphere.
The difference between Hekate and Fusee is that Hekate needs a custom configuration file that
tells Atmosphere what KIP's, Patches and other settings it should use, while Fusee boots them automatically.

- **NSP**

Nintendo Submission Package or NSP is either a dump of an installed title or a homebrew package that can be installed.

- **XCI**
 
NX Card Image or XCI is a dump from a Nintendo Switch cartridge.

- **NSZ**

A compressed NSP file.

- **XCZ**

A compressed XCI file.

- **NRO**

An extension that homebrew applications use.


