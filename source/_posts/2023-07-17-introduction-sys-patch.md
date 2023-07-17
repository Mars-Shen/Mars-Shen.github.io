---
layout: post
title: Switch硬件破解经验分享 - sys-patch 签名模块介绍
author: Mars Shen
multilingual: false
tags:
  - 原创
  - switch硬破教程
  - switch
published: true
subtitle: sys-patch 签名模块介绍，破解签名补丁的兜底方案
abbrlink: b2480a69
date: 2023-07-17 12:00:00
updated: 2023-07-17 12:00:00
---

>破解签名补丁的兜底方案。如果你签名补丁没法正常工作，可以试试这个

### sys-patch 签名模块介绍

ITotalJustice大神的最新作品，sys-patch是一个类似脚本的系统模块，可在启动时修补fs、es、ldr和nifm。它可以自动在系统更新后更新ldr补丁，因此即使没有更新sigpatches签名补丁，也可以保持最新的ldr补丁。

!!! info
    如果你正确安装了签名补丁，但你每次离线打开游戏时都提醒你“请关闭飞行模式”，因此导致很多游戏没法正确运行。那推荐你安装这个模块，可以解决这个问题，并保障大多数游戏正常运行。

个人测试在16.0.2版本下可以正常使用。
<!--more -->

###  使用方法:

下载zip包，解压后覆盖到SD卡根目录下，重启Switch，并重新安装签名有问题的游戏即可。
zip包地址为：[V1.4.2](https://github.com/ITotalJustice/sys-patch/releases/download/v1.4.2/sys-patch.zip)

---

> 引用

1. [https://github.com/ITotalJustice/sys-patch](https://github.com/ITotalJustice/sys-patch)
2. [gbatemp](https://gbatemp.net/threads/sys-patch-sysmod-that-patches-on-boot.633517/)


