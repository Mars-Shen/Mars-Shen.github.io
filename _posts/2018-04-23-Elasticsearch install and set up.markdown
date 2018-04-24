---
layout:     post
title:      "Ubuntu下Elasticsearch安装与问题记录"
subtitle:   ""
date:       2018-04-23 12:00:00
author:     "Mars Shen"
header-img: "img/Header/elasticsearch-hd.png"
catalog: true
multilingual: false
tags: [原创,互联网技术]
---

# Ubuntu下Elasticsearch安装与问题记录

> Elasticsearch 是一个分布式、可扩展、实时的搜索与数据分析引擎。 它能从项目一开始就赋予你的数据以搜索、分析和探索的能力。 --<<Elasticsearch: 权威指南>>

就像<code><a href="https://www.elastic.co/guide/cn/elasticsearch/guide/cn/preface.html">Elasticsearch: 权威指南</a></code>里说的，开源的 Elasticsearch是目前全文搜索引擎的首选。它可以快速地储存、搜索和分析海量数据。像维基百科、Stack Overflow、Github 等网站都采用它，足以见得他的能力。其实Elasticsearch最常见的组合是ELK(Elasticsearch + Logstash + kibana)，这里只介绍Elasticsearch的安装与使用。
本文的环境：Ubuntu。

#### 安装Elasticsearch注意事项
首先有一点要说明一下，ES对内存的要求很高，最好可以在内存2G以上的坏境运行Elasticsearch，否则可能会出现运行不稳定的问题。本人只有一台1核1G儿童云服务器，勉强使用中。另外ES运行需要Java8的运行环境，关于Java8的安装在这里不赘述，可以参考<code><a href="https://www.cnblogs.com/a2211009/p/4265225.html">这篇文章</a></code>。
#### 通过tar包安装Elasticsearch
这里用当前最新版6.2.4做例子，最新版可以从<code><a href="https://www.elastic.co/cn/products/elasticsearch">Elasticsearch官网</a></code>找到。

首先通过curl下载tar包：
{% highlight Shell %}
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz
{% endhighlight %}
解压到当前目录：
{% highlight Shell %}
tar -xvf elasticsearch-6.2.4.tar.gz
{% endhighlight %}
进入当前目录，启动Elasticsearch：
{% highlight Shell %}
cd elasticsearch-6.2.4/bin
./elasticsearch
{% endhighlight %}
如果是用root账号启动，会报以下错误：
{% highlight Java %}
Exception in thread "main" java.lang.RuntimeException: don't run elasticsearch as root. 
at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:93) 
at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:144) 
at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:285) 
at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:35) 
Refer to the log for complete error details.
{% endhighlight %}
这个意思也很明显，Elasticsearch出于安全方面的考虑， 不允许用户使用root账号启动Elasticsearch。我们得新建一个用户，专门用于启动Elasticsearch。

创建一个用户组elsearch与用户组中的用户elsearch：
{% highlight Shell %}
groupadd elsearch
useradd elsearch -g elsearch
{% endhighlight %}
修改用户elsearch的密码：
{% highlight Shell %}
passwd elsearch
{% endhighlight %}
修改目录拥有者，赋予相应的权限：
{% highlight Shell %}
chown -R elsearch:elsearch elasticsearch-6.2.4
{% endhighlight %}
切换到用户elsearch，或者使用elsearch登陆，启动Elasticsearch：
{% highlight Shell %}
su elsearch cd elasticsearch-6.2.4/bin
./elasticsearch
{% endhighlight %}
如果你想让你的ElasticSearch在后端启动：
{% highlight Shell %}
./bin/elasticsearch -d -p pid
{% endhighlight %}
相应的Log信息可以在<code>$ES_HOME/logs/</code>中找到。如果想关了后台的这个Elasticsearch进程，只要执行如下命令就可以了。
{% highlight Shell %}
kill `cat pid`
{% endhighlight %}
查看Elasticsearch是否安装成功，如果有返回值说明安装成功:
{% highlight Shell %}
curl http://127.0.0.1:9200
{% endhighlight %}

#### 通过Docker安装Elasticsearch
TBD

#### 安装Elasticsearch常见错误
这一部分部分引用<code><a href="https://github.com/DimonHo/DH_Note/issues/3">这篇文章</a></code>，结合我遇到的实际问题做了些修改。

问题1：
{% highlight Java %}
[2017-04-13T00:14:20,144][WARN ][o.e.b.JNANatives ] unable to install syscall filter:
java.lang.UnsupportedOperationException: seccomp unavailable: CONFIG_SECCOMP not compiled into kernel, CONFIG_SECCOMP and CONFIG_SECCOMP_FILTER are needed
{% endhighlight %}
- 原因：报了一大串错误，大家不必惊慌，其实只是一个警告，主要是因为你Linux版本过低造成的。一般如果VPS为OpenVZ构架的很有可能出现这个问题，因为主机的内核一般都很低而且你没法升级内核，Elasticsearch推荐内核版本为3.10以上。
- 解决方案：
	1. 重新安装新版本的Linux系统
	2. 警告不影响使用，可以忽略

问题2：
{% highlight Java %}
[2017-04-13T00:08:51,031][ERROR][o.e.b.Bootstrap ] [ZdbjA-a] node validation exception
[4] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
[2]: max number of threads [1024] for user [es] is too low, increase to at least [2048]
[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[4]: system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk
[2017-04-13T00:08:51,035][INFO ][o.e.n.Node ] [ZdbjA-a] stopping ...
[2017-04-13T00:08:51,097][INFO ][o.e.n.Node ] [ZdbjA-a] stopped
[2017-04-13T00:08:51,097][INFO ][o.e.n.Node ] [ZdbjA-a] closing ...
[2017-04-13T00:08:51,107][INFO ][o.e.n.Node ] [ZdbjA-a] closed
{% endhighlight %}
**注意：如果你的VPS为OpenVZ构架，可以不用接着往下看了，因为你什么也做不了，所以买VPS推荐买KVM构架的，KVM构架更加独立，在你自己手上的控制权更多。**

这里报了若干个错误，我们一个一个来
{% highlight Java %}
max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
{% endhighlight %}
- 原因：无法创建本地文件问题，用户最大可创建文件数太小
- 解决方案：
	1. 切换到root用户，编辑limits.conf配置文件，运行：<code>vi /etc/security/limits.conf</code>添加如下内容(备注：* 代表Linux所有用户名称（比如 hadoop）保存、退出、重新登录才可生效。)：
{% highlight Shell %}
* soft nofile 65536
* hard nofile 131072
* soft nproc 2048
* hard nproc 4096
{% endhighlight %}

{% highlight Java %}
max number of threads [1024] for user [es] is too low, increase to at least [2048]
{% endhighlight %}
- 原因：无法创建本地线程问题,用户最大可创建线程数太小
- 解决方案：
	1.切换到root用户，进入limits.d目录下，修改90-nproc.conf 配置文件。<code>vi /etc/security/limits.d/90-nproc.conf</code>找到如下内容：
{% highlight Shell %}
* soft nproc 1024
{% endhighlight %}
修改为：
{% highlight Shell %}
* soft nproc 2048
{% endhighlight %}

{% highlight Java %}
max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]
{% endhighlight %}
- 原因：这个问题比较常见，原因是因为最大虚拟内存太小
	1. 解决方案：切换到root用户下，修改配置文件sysctl.conf，<code>vi /etc/sysctl.conf</code>添加下面配置：
{% highlight Shell %}
vm.max_map_count=655360
{% endhighlight %}
并执行命令：<code>sysctl -p</code>，然后重新启动elasticsearch，即可启动成功。

问题3：

启动后，如果只有本地可以访问，尝试修改配置文件 elasticsearch.yml中network.host(注意配置文件格式不是以 # 开头的要空一格， ： 后要空一格)为
{% highlight Shell %}
network.host: 0.0.0.0
{% endhighlight %}
默认端口是 9200，注意：关闭防火墙 或者开放9200端口。

问题4：
{% highlight Java %}
Java HotSpot(TM) Client VM warning: INFO: os::commit_memory(0x74800000, 201326592, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (malloc) failed to allocate 201326592 bytes for committing reserved memory.
{% endhighlight %}
- 原因：这个错误是由于Elasticsearch尝试用默认配置的2G内存启动（Elasticsearch 5.X以后的版本），但你的机器实际内存小于2G导致的内存不足问题。
- 解决方案二选一即可：
	1. 升级你机器的内存。
	2. 修改Elasticsearch中jvm的配置，即修改<code>elasticsearch-6.2.4/config/</code>中的jvm.options文件。修改其中的<code>-Xms2g -Xmx2g</code>为<code>-Xms512m -Xmx512m</code>即可。注意这个参数可能需要根据具体情况具体设置，但有个原则就是<code>-Xms</code>与<code>-Xmx</code>需要想等


#### 安装x-pack插件
TBD

#### 资源列表
* <code><a href="https://www.elastic.co/guide/cn/elasticsearch/guide/cn/preface.html">Elasticsearch: 权威指南</a></code>， 这个是中文版的，容易看懂，但是有个问题就是版本有点老，最新版中已经移除type概念，这个指南中还存在这个概念，可以一看但更推荐看英文最新文档。
* <code><a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html">最新文档</a></code>，最新的英文文档，推荐阅读。






