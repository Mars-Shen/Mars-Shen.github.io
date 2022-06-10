---
layout: post
title: Switch硬件破解经验分享 - 破解后折腾篇
author: Mars Shen
multilingual: false
tags:
  - 原创
  - switch硬破教程
  - switch
published: true
subtitle: 破解后折腾教程
abbrlink: 20e16ead
date: 2022-06-10 12:00:00
updated: 2022-06-10 12:00:00
---
> 本文仅为本人对破解的理解以及相关实践的总结，仅供参考。

基于前面的文章，我们已经完成了硬破的所有工作，接下来的工作就是要让这台破解的机器真正跑起来了。

## 基础知识

安装完破解芯片后，每次重新开启switch主机，破解芯片将进行工作，将诱导主机载入我们自己的playload，通过这个自制playload，可以进一步引到主机运行自制固件。通常来说，目前主流的自制playload为Hekate，主流的自制固件为Atmosphere（也就是所谓的大气层固件）。所以我们现在要做的就是将整合了playload和大气层的文件整合包，塞到TF卡根目录。这个整合包可以自己做，也可以找网上别人做好的现成的整合包，下面简单介绍下如何制作整合包。

<!--more-->

## 前期准备

- **检查是否破解成功：** 如果机器正常破解，破解芯片正常安装，这时候按电源开机，应该提示 "NO SD"

!!! warning "注意"
    **不同的芯片固件可能会显示不同，但是都会提示类似没有插入TF卡。**

- **准备TF卡：** 将TF卡分区表设置成 MBR 格式，GPT 格式 Switch 无法识别，通过格式化分区为 exfat。

## 制作自己的整合包

!!! info "说明"
    **这里介绍快捷制作自己整合包的方法，完全手动整合就不进一步介绍了，各位有兴趣可以自行研究。**

首先制作基础整合包。这里介绍两种基础整合包获取方式

- **通过sdsetup网站获取自定义基础整合包**
  这个网站专门用来定制自己的整合包，这里面的固件、组件等都是最新版本的更新非常快。
  1. 进入([sdsetup官网下载页](https://www.sdsetup.com/console?switch))。
  2. 选择Recommended Defaults或自己选择自己需要的组件后拉到最下面点击Download your ZIP下载。
  3. 解压下载下来的zip包，你会看到2个文件夹，sd和payloads，将sd卡里面的内容全部复制到TF卡根目录，然后将payloads中```hekate_ctcaer_x.x.x.bin```更名成```playload.bin```放入TF卡根目录。

!!! info "关于Lockpick_RCM.bin"
    **payloads文件夹中```Lockpick_RCM.bin```可以用来备份主机key，有需要的同学可以自行搜索用法，这里就不赘述。**

- **通过 DeepSea 获取整合包**
  使用国外Neptune 团队的 DeepSea 整合包
  1. 进入（[DeepSea整合包下载页面](https://github.com/Team-Neptune/DeepSea/releases))下载最新的发布版本，三个版本随意 (我用的全功能版)。
  2. 解压放入tf卡根目录，并将```hekate_ctcaer_x.x.x.bin```更名成```playload.bin```。

完成上述两种方式之一后，将TF卡塞进switch主机，此刻开机后会进入 hekate 引导程序。
如果你是国产芯片，关机后会变成重启，进入hekate后设置```options->Auto HOS Power Off```即可。

### 备份 eMMC

Switch 内置的 eMMC 存储芯片有以下几个重要分区：BOOT0、BOOT1、GPP，GPP 内又包含 N 个子分区，比较重要的有两个 SYS (包含系统)、USER (包含用户数据)，USER 分区占用空间大约有 60G，因此一般不需要备份
1. 进入hekate选择```Tools->Backup eMMC```
2. 备份 ```eMMC BOOT0 & BOOT1 + eMMC SYS```。
3. 也可以完整备份，直接 ```eMMC RAW GPP```，或者 ```eMMC BOOT0 & BOOT1``` + ```eMMC SYS+eMMC USER```，两者等价。
4. 在hekate右下角选择```power off```关闭主机，拔出TF卡插入电脑，将TF卡中backup目录（备份文件）复制出保存在电脑上后，即可将TF卡中的backup目录删除。最后将TF卡插回主机，准备下一步操作。

### 制作虚拟系统

制作系统前先进入真实系统 ```Launch -> Stock (SYSNAND)```，从内置存储启动系统且不加载破解，删除所有 WiFi，并开启飞行模式，重启。
1. 进入hekate，选择```emuMMC->Create emuMMC->SD File```，制作一个基于TF卡文件的虚拟系统，完成后我们的双系统就制作完成了。

!!! info "这里稍微说下基于TF卡文件与基于TF卡分区的区别。"
    **基于SD卡分区的虚拟系统：**
    - 优点：
      出错的可能性最小。
    - 缺点：
      创建新的虚拟系统需要你对 SD 卡进行分区，因此 SD 上的现有文件将被删除。
      迁移到新的 SD 卡或备份虚拟系统要复杂得多。

    **基于 SD 文件的虚拟系统：**
    - 优点：
      非常容易传输或备份。
      创建新的虚拟系统更容易，并且不需要你对 SD 卡进行分区。
    - 缺点：
      你的虚拟系统设置损坏的可能性更高。
      这适用于FAT32和exFAT用户。
      无法通过系统设置在 SD 卡和虚拟系统之间传输游戏，这是一个没修复的bug。

2. 我们现在在Launch菜单中可以看到以下4个选项
   - **Stock (SYSNAND)：** 从机身内部的 eMMC 启动系统，不加载破解，也就是真实系统。
   - **CFW (SYSNAND)：** 从机身内部的 eMMC 启动系统，加载破解，不建议选择这一项，非常容易被ban。
   - **CFW (EMUMMC)：** 从 SD 卡的虚拟系统启动，加载破解，也就是虚拟系统，建议用此进入虚拟系统。
   - **Fusee：** 使用大气层playload引导，从 SD 卡的虚拟系统启动，加载破解，也就是大气层引导的虚拟系统。

### 处理虚拟系统

!!! info "准备下一步前"
    **关机并拔出 TF 卡，将TF卡连接电脑。**
	
- 删除虚拟系统序列号：
  1. 复制 ```atmosphere/config_templates/exosphere.ini``` 到 SD 卡根目录 ```blank_prodinfo_emummc``` 改为 1

!!! warning "注意"  
    **使用删除虚拟系统序列号并不能确保你不会被 Ban。**

- 虚拟系统屏蔽任天堂服务器
  1. 编辑 ```atmosphere/config/system_settings.ini``` 文件
  2. 取消 ```enable_dns_mitm = u8!0x1``` 前的注释
  3. 新建 ```atmosphere/hosts/emummc.txt``` 文件，文件内容如下：
    ```shell
        # Block Nintendo Servers
        127.0.0.1 *nintendo.*
        127.0.0.1 *nintendo-europe.com
        127.0.0.1 *nintendoswitch.*
        95.216.149.205 *conntest.nintendowifi.net
        95.216.149.205 *ctest.cdn.nintendo.net
    ```

- 打签名破解补丁：
  1. 下载[ITotalJustice/patches](https://github.com/ITotalJustice/patches)，直接解压到根目录，这样才能运行破解游戏。
  2. 编辑 ```bootloader/hekate_ipl.ini``` 文件，在[CFW (EMUMMC)] 下新增一行 ```kip1patch=nosigchk```，我只在虚拟系统下开启破解补丁。

!!! warning "注意"
    **大气层默认是不允许运行破解软件的，需要额外添加签名破解补丁才可以运行破解软件。**

!!! info "关于启动虚拟系统的方式"
    **如果使用Fusee方式引导进入大气层，则不需要添加```kip1patch=nosigchk```。**

- 金手指下载：
  1. 进入[gbatemp.net](https://gbatemp.net/download/cheat-codes-sxos-and-ams-main-cheat-file-updated.36311/)，下载金手指内容。
  2. 将下载的金手指内容解压到 ```atmosphere/contents/``` 目录

- 游戏下载：
  1. [nxbrew.com](https://nxbrew.com/)
  2. [switch520](https://switch520.com/)
  3. [rpgonly.com](https://rpgonly.com/)
  4. [switchvip](https://switchvip.com/)
  
开机，Enjoy～

### 使用原则
- 只在虚拟系统下安装破解游戏及 NSP 格式的插件
- 真实系统只插卡带玩或下载正版数字版游戏
- 真实系统可联机的游戏联机时不要开金手指，除非你有把握
- 真实系统可联机的游戏存档不要改的太过分，除非你有把握
- 按照此原则，任何系统随意联网，随意联机游戏，目前我还没被 ban，被 ban 了会在此更新，如被 ban 号或 ban 机，本人概不负责

### 其他有用的插件

[NS-USBloader](https://github.com/developersu/ns-usbloader)：[PC 端] 使用 USB 数据线连接 Switch 安装破解游戏时，需要在电脑上安装此软件，另外还需要 Java 运行环境
[NxNandManager](https://github.com/eliboa/NxNandManager)：[PC 端] 虚拟系统占用了 64G 的 SD 卡空间，可以用这个软件缩小 USER 分区占用的空间 (只需要存游戏存档就可)，具体教程搜索互联网。
[NSCbuilder](https://github.com/julesontheroad/NSC_BUILDER/releases): [PC 端] 魔改ROM神器，可以整合，转换，提取rom，具体教程搜索互联网。
[Lockpick_RCM](https://github.com/shchmue/Lockpick_RCM)：[Switch 端・引导界面] 用于提取本机密钥，在 Hekate 这个 BootLoader 的引导界面下使用
[90DNS Tester](https://github.com/meganukebmp/Switch_90DNS_tester)：[Switch 端] 确定当前系统有无屏蔽任天堂服务器
[Goldleaf](https://github.com/XorTroll/Goldleaf)：[Switch 端] 虚拟系统要和任天堂服务器确认后才能退出并删除不用的账号，这是做不到，金叶子还有这个附加功能可以删除账号
[另一个金手指发布地址](https://github.com/ibnux/switch-cheat)

### 更新系统

等待 [Hekate](https://github.com/CTCaer/hekate) 这个 BootLoader，[大气层系统](https://github.com/Atmosphere-NX/Atmosphere/releases)，[破解补丁](https://github.com/ITotalJustice/patches)更新，确保三者已支持最新系统，或直接等整合包 DeapSea 更新

1. 正确安装上述三者
2. 真实系统：开机进入 Stock (SYSNAND)，直接联网并更新，等待自动重启
3. 虚拟系统：
4. 下载固件：[https://darthsternie.net/switch-firmwares/](https://darthsternie.net/switch-firmwares/)
5. 解压到根目录
6. 开机进入 ```CFW (EMUMMC)```, 进入相册 ->Daybreak->Install-> 选择固件目录，一路下一步，成功后自动重启。

---
> 引用

1. [OLED Switch 硬破后的折腾攻略](https://blog.kuretru.com/posts/4d5dc8ff/)
2. [Switch硬件破解经验分享 - 术语篇](https://www.marsshen.com/2022/05/29/switch-hack-terminology/)

