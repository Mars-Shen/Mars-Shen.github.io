---
layout:     post
title:      "Ubuntu下RabbitMQ的安装与配置"
subtitle:   ""
date:       2018-05-05 12:00:00
author:     "Mars Shen"
header-img: "img/Header/rabbitmq-install-hd.png"
catalog: true
multilingual: false
tags: [原创,互联网技术,RabbitMQ,Ubuntu]
---

# Ubuntu下RabbitMQ的安装与配置

最近在研究RabbitMQ，本文简单记录了如何搭建与配置一个RabbitMQ服务器。

## 安装RabbitMQ
<code>rabbitmq-server</code>是可以在Ubuntu系统与Debian系统中使用的RabbitMQ服务器。在Ubuntu标准的repositories中，其实包含了<code>rabbitmq-server</code>，但是标准仓库中的版本往往非常的老旧，直接安装的话会得到的可能不是你想要的版本，所以想安装新版本，我们需要一些额外的工作。这里主要介绍了Ubuntu下通过<code>apt-get</code>的方法安装最新版本，如果想了解更多的安装方法，可以看<code><a href="https://www.rabbitmq.com/download.html">这里</a></code>

**安装前提**

由于RabbitMQ需要基于Erlang/OTP，所以在安装RabbitMQ之前需要先安装Erlang/OTP。同样的，在Ubuntu标准的repositories中，Erlang/OTP的版本很老，推荐不要直接安装在Ubuntu标准的repositories中Erlang/OTP，而是安装新版本。

#### 安装Erlang
**添加地址**

首先执行下面两个命令，添加Erlang自己的仓库地址到你本地。
{% highlight Shell %}
wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
sudo dpkg -i erlang-solutions_1.0_all.deb
{% endhighlight %}

或者你可以手动添加仓库地址至本地。
添加下面代码至你本地<code>/etc/apt/sources.list</code>中，注意把下面的<code>{distribution}</code>(包括大括号)替换成你自己系统的distribution。
{% highlight Shell %}
deb https://packages.erlang-solutions.com/ubuntu {distribution} contrib
{% endhighlight %}
如果不知道自己系统的distribution是什么，可以输入<code>lsb_release -c</code>查看，我自己的系统是Ubuntu16.04，对应的distribution是xenial，所以我添加进<code>/etc/apt/sources.list</code>的命令是
{% highlight Shell %}
deb https://packages.erlang-solutions.com/ubuntu xenial contrib
{% endhighlight %}
下一步用下面的命令来添加Erlang公钥
{% highlight Shell %}
wget https://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc
sudo apt-key add erlang_solutions.asc
{% endhighlight %}
**开始安装Erlang**

用下面的命令刷新本地apt-get仓库的缓存，然后安装Erlang。
{% highlight Shell %}
sudo apt-get update
sudo apt-get install erlang
{% endhighlight %}
至此，Erlang安装完毕，如果想了解更多Erlang/OTP的安装方法，可以看<code><a href="https://packages.erlang-solutions.com/erlang/#tabs-debian">这里</a></code>

#### 安装rabbitmq-server
**添加rabbitmq仓库地址**

同样的下面的像上文所说的一样将下面的<code>{distribution}</code>(包括大括号)替换成你自己系统的distribution。
{% highlight Shell %}
echo "deb https://dl.bintray.com/rabbitmq/debian {distribution} main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list
{% endhighlight %}
添加rabbitmq仓库公钥
{% highlight Shell %}
wget -O- https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc | sudo apt-key add -
{% endhighlight %}
用下面的命令刷新本地apt-get仓库的缓存，然后安装rabbitmq-server。
{% highlight Shell %}
sudo apt-get update
sudo apt-get install rabbitmq-server
{% endhighlight %}
至此rabbitmq-server安装完毕。

## 运行与配置RabbitMQ
#### 运行RabbitMQ

在RabbitMQ安装包安装完后，服务器默认是会在后台通过一个没有特权的用户rabbitmq来运行的，想要手动启动或者停止重启服务器，只需要像系统中别的启动命令一样就可以了
{% highlight Shell %}
service rabbitmq-server start
{% endhighlight %}

#### 常用端口
以下是官网上常用端口的介绍:

**4369**: epmd, a peer discovery service used by RabbitMQ nodes and CLI tools <br>
**<code>5672, 5671</code>**: used by AMQP 0-9-1 and 1.0 clients without and with TLS <br>
**25672**: used for inter-node and CLI tools communication (Erlang distribution server port) and is allocated from a dynamic range (limited to a single port by default, computed as AMQP port + 20000). See networking guide for details. <br>
**35672-35682**: used by CLI tools (Erlang distribution client ports) for communication with nodes and is allocated from a dynamic range (computed as Erlang dist port + 10000 through dist port + 10010). See networking guide for details. <br>
**<code>15672</code>**: HTTP API clients and rabbitmqadmin (only if the management plugin is enabled) <br>
**61613, 61614**: STOMP clients without and with TLS (only if the STOMP plugin is enabled) <br>
**1883, 8883**: (MQTT clients without and with TLS, if the MQTT plugin is enabled <br>
**15674**: STOMP-over-WebSockets clients (only if the Web STOMP plugin is enabled) <br>
**15675**: MQTT-over-WebSockets clients (only if the Web MQTT plugin is enabled) <br>

其中<code>5672, 5671</code>为消息队列常用的端口，<code>15672</code>为网页可视化管理所用的端口。

#### 开启网页可视化管理
开启网页可视化管理很简单，首先需要开启RabbitMQ可视化管理插件，通过如下命令开启
{% highlight Shell %}
rabbitmq-plugins enable rabbitmq_management
{% endhighlight %}
然后访问<code>http://server-name:15672/</code>可以进入管理页面，默认账号密码都为<code>guest</code>，里面可以管理像创建用户，维护队列之类的功能，非常好用。
详细信息可以看<code><a href="https://www.rabbitmq.com/management.html">这里</a></code>

完

