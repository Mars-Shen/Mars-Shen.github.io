---
layout: post
title: Switch硬件破解经验分享 - 术语篇
author: Mars Shen
multilingual: false
tags:
  - 原创
  - switch硬破教程
  - switch
published: true
abbrlink: f96d7ec4
date: 2022-05-29 12:00:00 
updated: 2022-05-29 12:00:00
---
### **RCM**

  RCM 是 ReCovery Mode 的缩写, 即恢复模式.

### **Horizon OS (HOS)**

  Horizon 是 Nintendo Switch 操作系统的名称.

### **OFW**

  OFW 是 Original Firmware的缩写, 即官方原版胸固件.

  这指的是你设备中没有被修改的原始系统固件.
<!-- more -->
### **CFW**

  CFW 是 Custom Firmware 的缩写, 即自制系统固件.

  这指的是被修改的自制系统固件, 第三方自制程序(homebrew)被允许在此类系统固件上运行.

### **Homebrew**

  一般用于描述开发人员为锁定的(非开放的)系统创建的非官方软件.

  对于 Switch 来说, 此类软件可以包括存档管理器, 模拟器或破解游戏.

### **Atmosphere**

  大气层自制固件(Atmosphere), 一个免费开源的第三方自制固件(CFW), [Github地址](https://github.com/Atmosphere-NX/Atmosphere).

### **SXOS**

  由 Team Xecuter 创建的付费并封闭源代码的自制固件(CFW), 由于TX团队已经由于破解盈利被抓捕, 所以相关固件也已经不再更新.

  不适用于 11.0.0 以上的固件.

### **NAND**

  NAND是switch设备中内置的存储(不要与 SD 卡存储混淆).

  Switch 一般拥有 32GB 内置存储(25.9GB 可用存储, 其余被系统占用).

### **SysNAND**

  SysNAND 是 "System NAND" 的缩写, 即设备机身内置存储.

  SysNAND中的内容是存储在机身内置存储中的内容(不要与OFW混淆).

  它包含从操作系统本身到游戏、屏幕截图、视频和保存文件的所有内容.

  无论是被破解或未被破解的设备都有一个SysNAND.

  我们常说的正版系统一般就存储在此.

### **EmuNAND**

  EmuNAND 是 "Emulated NAND"的缩写, 即虚拟内置存储.

  EmuNAND 是 SysNAND的副本，它存储在 SD 卡上，而不是在设备内部存储器上.

  可以把它想象成在你的 SD 卡上 有一个独立的额外的Nintendo Switch. 

  一般在 EmuNAND 中的任何更改或操作都不会影响您的 SysNAND，因为它是一个独立的副本。

  我们常说的虚拟系统一般就存储在此.

!!! warning "注意"
    **使用 EmuNAND 并不能确保你不会被Ban。 单击此处了解有关如何避免被Ban的更多信息。**

### **Signature Patches**

  Signature Patches(签名补丁)或 SigPatches 允许您的设备绕过 Horizon 对已安装程序执行的签名检查.

### **Hekate**

  一个免费开源的自定义引导加载程序(bootloader)，可让您绕过熔断数检查, 并且其拥有许多有用的工具. [Github地址](https://github.com/CTCaer/hekate).

  可用于启动 OFW、CFW、Android、Linux 和任何其他有效payload.

### **Fusee**

  一个迷你自定义引导加载程序(bootloader), 仅用于引导大气层自制固件(Atmosphere).
  Hekate 和 Fusee 之间的区别在于, Hekate 需要一个自定义配置文件来告诉Atmosphere它应该使用哪些 KIP、补丁和其他设置, 而Fusee会自动启动它们.

### **NSP**

  NSP 是 Nintendo Submission Package 的缩写.

  NSP是已安装title的转储安装程序包或可以安装的自制程序包. eShop中的数字版一般都为此格式.

### **XCI**

  XCI 是 NX Card Image 的缩写.

  XCI 是 switch 卡带dump文件镜像. switch 卡带中文件一般为此格式.

### **NSZ**

  一种压缩后的 NSP 文件.

### **XCZ**

  一种压缩后的 XCI 文件.

### **NRO**

  homebrew 应用程序使用的扩展.
  
### **sx core/lite**

  TX小组研发的硬件破解芯片，目前小组成员被抓，相关产品停产，固件不再更新。

### **HWFLY**

  国产芯片名称, 截止目前最新版本为第四代芯片。[官网](https://www.chipnx.com/)

  其原理为开机触发脉冲漏洞破解 switch 主机，只要破解后芯片就不再工作, 后续工作交给 tf 卡上的自定义引导加载程序(bootloader), 只有关机后下次开机才需要重新触发。

### **Spacecraft-NX**

  开源的破解芯片固件，[Github地址](https://github.com/Spacecraft-NX/firmware)

  switch oled之后的版本请务必刷0.2以后的固件版本。

### **hwfly-nx**

  开源的破解芯片固件， 由于Spacecraft-NX更新缓慢，一些开发者开发基于Spacecraft-NX创建了hwfly-nx固件。 [Github地址](https://github.com/hwfly-nx)
