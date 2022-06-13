---
layout: post
title: Switch硬件破解经验分享 - 破解问题FAQ
author: Mars Shen
multilingual: false
tags:
  - 原创
  - switch硬破教程
  - switch
published: true
subtitle: 破解后常见问题解答
abbrlink: c7924c66
date: 2022-06-13 12:00:00
updated: 2022-06-13 12:00:00
---
> 本文仅为本人对破解的理解以及相关实践的总结，仅供参考。

!!! info "说明"
    **我会收集一些硬破后常见错误解决方式，还有一些技巧更新在这个页面。有什么问题也可以在这里留言。**

### 如何进入homebrew launcher(自制软件启动器，简称HBL)
有两种方法可以进入homebrew launcher：
- 随便找个游戏，按住R并启动这个游戏。
或者
- 通过单击相册图标
  - 这将使你进入applet mode(小程序模式)，你会在右上角看到applet mode几个字。

!!! info "关于applet mode"
    **applet mode(小程序模式)并没有用最高权限执行自制软件，可能会使某些自制程序可能无法正常工作出错。**
<!--more -->
### 如何进入维护模式
如果你经常打开游戏报错，或者遇到莫名其妙的问题，那么可以尝试按如下步骤进入维护模式：

1. 打开switch。
2. 系统开机，进入Hekate后选择你要进的系统，进入大气时(黑屏时)按住按住(音量 +) 和 (音量 -) 直到出现画面。
3. 这时候你就进入了维护模式，一般来说什么都不要动，直接长按电源键关机，重新打开switch即可。

### 如何在进入游戏时禁止Mods和金手指
如果想在启动游戏时禁用Mods或金手指，请按住L并启动你想要启动的游戏。

### 启动菜单中虚拟系统消失
如果启动菜单中虚拟系统消失，请检查```sd -> bootloader -> hekate_ipl.ini```配置文件，查看是否缺少虚拟系统配置。可以参考如下配置：

```
...省略之前配置
{}
[Fusee]
emummcforce=1
icon=bootloader/res/atmosphere-emu.bmp
payload=bootloader/payloads/fusee.bin

{}
[Atmosphere (emuMMC)]
emummcforce=1
fss0=atmosphere/package3
kip1patch=nosigchk
icon=bootloader/res/atmosphere-emu.bmp
```
其中```{}```为分隔符，```[Fusee]```为fusee.bin方式引导的大气层虚拟系统，```[Atmosphere (emuMMC)]```为普通方式引导的大气层虚拟系统。

### 哪些行为可以让我被ban？

!!! error "注意"
    **任天堂的政策一直在动态变化，我不保证下面说的一定对。如被 ban 号或 ban 机，本人概不负责。**

立刻ban：
- 盗版行为：只要使用盗版软件并且联网，必ban，包括但不限于安装盗版游戏、安装自制 NSP 文件等。
- 使用自制软件更改用户图标。
- 不合规的 eShop 行为，包括退款，黑卡等，前段时间闹的沸沸扬扬的港服Ban号就是由于黑点卡引起的。
- 使用带有你switch唯一证书的自制软件或 PC 工具连接到任何任天堂网络或者CDN。

很有可能被ban，但一般不会马上被ban：
- 修改网络游戏
- 在网络游戏中作弊
- 使用自制软件清除系统日志
- 在线使用多个 NAND（sysMMC 和 emuMMC），而并且连接了任天堂的网络。

到目前为止，不会被Ban：

- 单纯使用大气层自制固件(这里指的是单纯的使用大气层固件，没有任何上述的盗版行为)。
- 单纯使用Homebrew。
- 使用自定义主题或者自定义系统模块。
- 单纯单机离线游戏中使用Mods或者使用金手指。
- 超频
- 运行其他操作系统，例如 Android 或 Linux

!!! info "综上所述，按如下原则使用破解switch"
    - **只在虚拟系统下安装破解游戏及 NSP 格式的插件**
    - **真实系统只插卡带玩或下载正版数字版游戏**
    - **真实系统可联机的游戏联机时不要开金手指，除非你有把握**
    - **真实系统可联机的游戏存档不要改的太过分，除非你有把握**
    - **目前我还没被 ban，被 ban 了会在此更新，如被 ban 号或 ban 机，本人概不负责**

### 破解芯片一直亮黄灯？

大概率是焊接问题，请检查焊接焊点。
	
---

> 引用

1. [switch.homebrew.guide](https://switch.homebrew.guide/faq.html)
2. [rentry.org](https://rentry.org/SwitchFAQ)
3. [nh-server.github.io](https://nh-server.github.io/switch-guide/faq/)
