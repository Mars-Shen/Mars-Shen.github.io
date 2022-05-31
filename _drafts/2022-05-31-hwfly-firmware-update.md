---
layout: post
title: "Switch硬件破解经验分享 - 破解芯片更新篇"
subtitle: ""
date: 2022-05-31 12:00:00
author: "Mars Shen"
header-img: "img/Header/coder-doom-dos-code-hd.jpg"
catalog: true
multilingual: false
tags: [原创,switch硬破教程,switch]
published: false
---
## Switch硬件破解经验分享 - 破解芯片更新篇

### 基础知识

- 目前市面上能接触到的较多的是hwfly芯片，即国产芯片，本文主要介绍续航版国产芯片升级hwfly-nx固件，其余版本芯片升级或升级别的固件请酌情参考。

- 目前国产芯片已经出了好几代，第四代芯片是可以对芯片的固件进行更新或者改刷别的开源固件的。

- 如何判断当前芯片是否为国产四代芯片:

  芯片左上角为MV1-7以上,即为四代或更新的芯片, 如下图

   ![四代](https://raw.githubusercontent.com/sthetix/DIAGRAM/main/Flashable-Core-SWD.jpg)

### hwfly-nx固件介绍

  hwfly-nx是一个完全开源的芯片固件，用于链式引导您自己的playload，其支持SX Core / Lite/ hwfly等芯片

#### 使用

  - Place a compatible payload, i.e. [hekate](https://github.com/CTCaer/hekate/releases), in your sdcard root folder under name `payload.bin`.
  - After initial training (up to 5 minutes), modchip is ready and should boot quickly.
  - Holding VOL+ during boot prevents the modchip from going to sleep. This subsequently allows interactions with [hwfly-toolbox](https://github.com/hwfly-nx/hwfly-toolbox) for additional modchip operations and diagnostics.
  - When glitching is successful but an sdcard issue prevent loading of payload.bin, diagnostics are provided on the screen. From this screen, OFW can be booted by pressing VOL+ & VOL- simultaneously.
  - Holding VOL+ and VOL- during power-on bypasses any payloads and boots into OFW. Buttons must be held until glitching completes.

#### LED诊断

  After installation, the firmware will train itself. This can be recognized by the yellow pulsing LED pattern and can take up to 5 minutes. When completed, subsequent boots will be much faster. Refer to the diagram below for a full overview of possible LED patterns, which may aid in diagnosis in case of non-working installations.

  ![诊断](https://camo.githubusercontent.com/e8cfc33ee7e8bd08187148d993425f666bd009369495233e25929e1ca79eee67/68747470733a2f2f692e696d6775722e636f6d2f6e48596e4266752e676966)

#### 更新

  Updating can be done using one of 3 methods:

  **Method a) - Using USB bootloader (preferred)**

  1. Fully power of console.
  2. Insert USB cable. Take extreme caution not to insert incorrectly, this fully kills USB functionality on the chip.
  3. Run flash.bat. This updates both bootloader and firmware.
  4. Remove USB cable.
  5. Power on console.

  **Method b) **- Using hwfly-toolbox This method does not require opening device, but it cannot flash the bootloader. LED patterns in bootloader will not correspond with documented ones.

  1. Obtain hwfly toolbox from https://github.com/hwfly-nx/hwfly-toolbox/releases
  2. Place hwfly_toolbox.bin in sdcard:/bootloader/payloads/.
  3. Boot switch into hekate, then run hwfly_toolbox.bin payload.
  4. Update SD loader from toolbox menu.
  5. Power off console from menu.
  6. Place firmware.bin and sdloader.enc from hwfly firmware into sdcard root. Do not do so earlier. If you have previously flashed a beta firmware, also create empty file .force_update in sdcard root.
  7. Put SD in switch and power on while holding VOL+ (verify: modchip green light keep pulsing while hekate loads).
  8. Run hwfly_toolbox from hekate again.
  9. Update firmware from menu.
  10. Reboot & done.

  **Method c) - External programmer (j-link, st-link, gd-link)** See https://www.sthetix.info/flashing-updating-unbricking-the-chip/ for details. Supplied bootloader.bin should be flashed at 0x08000000. Supplied payload.bin should be flashed at 0x08003000.

  Note: neither method will allow unflashable chips to become flashed. Unflashable ones have a BGA FPGA IC on the board. Flashable ones use a QFN FPGA.
