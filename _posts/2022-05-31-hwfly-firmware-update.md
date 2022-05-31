---
layout: post
title: Switch硬件破解经验分享 - 破解芯片更新篇
date: 2022-05-31T12:00:00.000Z
author: Mars Shen
header-img: img/Header/coder-doom-dos-code-hd.jpg
catalog: true
multilingual: false
tags:
  - 原创
  - switch硬破教程
  - switch
published: true
subtitle: ''
---

## Switch硬件破解经验分享 - 破解芯片更新篇

### 基础知识

- 目前市面上能接触到的较多的是hwfly芯片，即国产芯片，本文主要介绍续航版国产芯片升级hwfly-nx固件，其余版本芯片升级或升级别的固件请酌情参考。

- 目前国产芯片已经出了好几代，第四代芯片是可以对芯片的固件进行更新或者改刷别的开源固件的。

- 如何判断当前芯片是否为国产四代芯片:

  芯片左上角为MV1-7以上,即为四代或更新的芯片, 如下图

   ![四代](https://raw.githubusercontent.com/sthetix/DIAGRAM/main/Flashable-Core-SWD.jpg)

### hwfly-nx固件介绍

hwfly-nx（[最新固件下载地址](https://github.com/hwfly-nx/firmware/releases/latest)）是一个完全开源的芯片固件，用于链式引导您自己的playload，其支持SX Core / Lite/ hwfly等芯片

#### 使用

- 将兼容的有效playload（如 [hekate](https://github.com/CTCaer/hekate/releases) ），重命名payload.bin，并放在您的 sd 卡根目录中。
- 第一次开启时，芯片将进行初始脉冲训练（最多 5 分钟），完成后破解芯片将准备就绪，后续就可以更快速的启动。
- 在启动期间按住 VOL+ 可防止破解芯片进入睡眠状态（一般破解完成后，破解芯片就会关闭，LED灯会熄灭）。这操作完成后可以允许破解芯片与[hwfly-toolbox](https://github.com/hwfly-nx/hwfly-toolbox)交互以进行额外的对破解芯片的 操作和诊断。
- 启动switch后，当芯片脉冲破解成功但由于 sd 卡问题阻止加载 payload.bin 时，屏幕上会提供诊断。在这时， 可以通过同时按下 VOL+ 和 VOL- 来启动OFW。
- 在开机期间按住 VOL+ 和 VOL- 直到脉冲完成（绿灯亮起），会绕过任何有效的playload 并启动到 OFW。

#### LED诊断

  安装完成后的第一次启动，固件将自动进行脉冲破解训练。这时候主板上的LED灯呈黄色闪烁状态，最多可能需要 5 分钟。当完成第一次脉冲破解训练后，后续引导会快得多。有关可能的 LED 模式的完整概述，请参阅下图，这可能有助于对芯片安装完成后但不工作的情况下进行诊断。

![诊断](https://camo.githubusercontent.com/e8cfc33ee7e8bd08187148d993425f666bd009369495233e25929e1ca79eee67/68747470733a2f2f692e696d6775722e636f6d2f6e48596e4266752e676966)

#### 更新

  可以使用以下 3 种方法之一进行更新破解芯片：

**方法 a) - 使用 USB 引导加载程序（首选）**

1. 完全关闭你的switch主机。
2. 在**芯片上**插入 USB 电缆。**注意不要插入错误！！！ 不要插反！**如果插反这会完全破坏芯片上的 USB 功能。
3. 运行[最新固件下载地址](https://github.com/hwfly-nx/firmware/releases/latest)中的flash.bat。这会更新引导加载程序（bootloader）和破解固件（firmware），运行过完成后LED灯呈蓝色闪烁状态。
4. 拔下 USB 电缆。
5. 重新打开你的switch主机。

**方法 b) - 使用 hwfly-toolbox 进行更新操作，该方法不需要打开设备后盖，但不能刷入引导程序（bootloader）。此时引导加载程序（bootloader）中的 LED 状态可能与上面LED状态图不一致。**

1. 从此获取[hwfly-toolbox](https://github.com/hwfly-nx/hwfly-toolbox/releases)刷固件工具
2. 将 hwfly_toolbox.bin 插入 sd卡的/bootloader/payloads/ 中。
3. 切换引导到 hekate，选择playload然后运行 hwfly_toolbox.bin。
4. 从hwfly_toolbox的菜单中更新SD loader。
5. 从菜单关闭switch主机。
6. 取出sd卡，将 firmware.bin和sdloader.enc 放到您的sd卡根目录。**不要提早放**。如果之前刷过测试版本的固件，需要创建一个名为.force_update的空文件到sd卡根目录。
7. 将sd卡放回switch主机，按住VOL+按钮开机（如果进入hekate后绿灯一直亮，说明操作成功。
8. 再次从 hekate 运行 hwfly_toolbox。
9. 从菜单更新固件（firmware）。
10. 重新启动switch并完成更新。

**方法c) - 外部编程器应用j-link、st-link、gd-link）** 

1. 参见[刷固件方法地址](https://www.sthetix.info/flashing-updating-unbricking-the-chip/)。
2. 提供的bootloader.bin应该在0x080000000处被刷入。提供的payload.bin应在0x08003000处被刷入。


**注意**：上面的三个方法没有一个可将无法更新固件的4代以前的芯片变成可更新固件的芯片。（不可更新固件的芯片在主板上使用一个 BGA FPGA类型控制芯片，而可更新固件的芯片主板上使用的是QFN FPGA类型控制芯片，硬件上是不通的）。



###### 引用：

1. [固件Github地址](https://github.com/hwfly-nx/firmware)
