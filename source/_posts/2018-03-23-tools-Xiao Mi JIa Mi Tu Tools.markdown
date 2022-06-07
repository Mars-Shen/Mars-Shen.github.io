---
layout: post
title: 自动化抢小米加密兔辅助工具
subtitle: ''
author: Mars Shen
header-img: img/Header/jiamitu-hd.jpg
catalog: true
multilingual: false
tags:
  - 原创
  - 区块链
abbrlink: a9eae027
date: 2018-03-23 12:00:00
---

# 自动化抢小米加密兔辅助工具

<p>
小米区块链宠物小米加密兔领养活动目前第一阶段已经结束,请等待官方第二阶段消息. 该工具脚本用于实现自动化抢小米加密兔, 目前为可用状态(2018/4/2), 但并不能保证此脚本长期可用, 若有问题或者建议请在下面留言.
</p>

#### 使用方法
* 使用前请先在Chrome下载Tampermonkey插件.
* 下载脚本文件XiaoMi-JiaMiTu-Tools.zip至本地.
* 将文件XiaoMi-JiaMiTu-Tools.zip导入Tampermonkey.
* 前往 <code><a href="https://jiamitu.mi.com/home">https://jiamitu.mi.com/home</a></code> 小米加密兔领养页面并登陆你自己的账号.
* 确保当前页面Tampermonkey中我们的脚本为开启状态. 
* 当活动时间开始前1分钟点击**开始抢加密兔**,然后耐心等待脚本运行. 当页面提示你已抢到, 说明抢领成功, 如果长时间没领到加密兔, 可能加密兔已经被领完, 请第二天再尝试.
* 如果需要更新, 请在Tampermonkey插件更新插件.

下载链接: <code><a href="https://github.com/Mars-Shen/XiaoMi-JiaMiTu-Tools/raw/master/XiaoMi-JiaMiTu-Tools.zip">点我下载</a></code> 
源码地址: <code><a href="https://github.com/Mars-Shen/XiaoMi-JiaMiTu-Tools">点我查看</a></code>
<!-- more -->

#### 脚本原理
<p>
脚本模拟用户点击, 运行时循环向小米服务器发用户送点击请求.
</p>

#### 注意事项
* 活动开始时, 运行中脚本可能会卡住, 日志可能会不输出, 这是正常情况, 因为小米服务器在那一刻接受大量请求, 响应缓慢. 所以抢不抢的到兔子, 还是得看你运气, 这个脚本并不能保证你能抢到兔子, 只是起到辅助作用.
* 本脚本不会获取你的个人信息并且开源, 想了解具体信息请查看上面源码链接中代码.

<!-- 开启前效果:
<img src="{{ site.baseurl }}/img/jiamitu/jiamitu-0.png" width="80%" heigh="80%"> -->
开启后效果:
<img src="{{ site.baseurl }}/img/jiamitu/jiamitu-1.png" width="70%" heigh="70%">
