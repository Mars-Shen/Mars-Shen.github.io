---
layout: post
title: Ubuntu下Elasticsearch安装与问题记录
subtitle: ''
author: Mars Shen
multilingual: false
tags:
  - 原创
  - 互联网技术
  - ELK
  - Ubuntu
abbrlink: 2492827d
date: 2018-04-23 12:00:00
updated: 2018-04-23 12:00:00
---

# Ubuntu下Elasticsearch安装与问题记录

> Elasticsearch 是一个分布式、可扩展、实时的搜索与数据分析引擎。 它能从项目一开始就赋予你的数据以搜索、分析和探索的能力。 --<<Elasticsearch: 权威指南>>

就像<code><a href="https://www.elastic.co/guide/cn/elasticsearch/guide/cn/preface.html">Elasticsearch: 权威指南</a></code>里说的，开源的 Elasticsearch是目前全文搜索引擎的首选。它可以快速地储存、搜索和分析海量数据。像维基百科、Stack Overflow、Github 等网站都采用它，足以见得他的能力。其实Elasticsearch最常见的组合是ELK(Elasticsearch + Logstash + kibana)，这里只介绍Elasticsearch的安装与使用。
本文的环境：Ubuntu。

#### 安装Elasticsearch注意事项
首先有一点要说明一下，ES对内存的要求很高，最好可以在内存2G以上的坏境运行Elasticsearch，否则可能会出现运行不稳定的问题。本人只有一台1核1G儿童云服务器，勉强使用中。另外ES运行需要Java8的运行环境，关于Java8的安装在这里不赘述，可以参考<code><a href="https://www.cnblogs.com/a2211009/p/4265225.html">这篇文章</a></code>。

#### 通过tar包安装Elasticsearch
这里用当前最新版6.2.4做例子，最新版可以从<code><a href="https://www.elastic.co/cn/products/elasticsearch">Elasticsearch官网</a></code>找到。
<!-- more -->
首先通过curl下载tar包：
```shell
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz
```
解压到当前目录：
```shell
tar -xvf elasticsearch-6.2.4.tar.gz
```
进入当前目录，启动Elasticsearch：
```shell
cd elasticsearch-6.2.4/bin
./elasticsearch
```
如果是用root账号启动，会报以下错误：
```shell
Exception in thread "main" java.lang.RuntimeException: don't run elasticsearch as root. 
at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:93) 
at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:144) 
at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:285) 
at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:35) 
Refer to the log for complete error details.
```
这个意思也很明显，Elasticsearch出于安全方面的考虑， 不允许用户使用root账号启动Elasticsearch。我们得新建一个用户，专门用于启动Elasticsearch。

创建一个用户组elsearch与用户组中的用户elsearch：
```shell
groupadd elsearch
useradd elsearch -g elsearch
```
修改用户elsearch的密码：
```shell
passwd elsearch
```
修改目录拥有者，赋予相应的权限：
```shell
chown -R elsearch:elsearch elasticsearch-6.2.4
```
切换到用户elsearch，或者使用elsearch登陆，启动Elasticsearch：
```shell
su elsearch cd elasticsearch-6.2.4/bin
./elasticsearch
```
如果你想让你的ElasticSearch在后端启动：
```shell
./bin/elasticsearch -d -p pid
```
相应的Log信息可以在<code>$ES_HOME/logs/</code>中找到。如果想关了后台的这个Elasticsearch进程，只要执行如下命令就可以了。
```shell
kill `cat pid`
```
查看Elasticsearch是否安装成功，如果有返回值说明安装成功:
```shell
curl http://127.0.0.1:9200
```

#### 通过Docker安装Elasticsearch
通过Docker来安装Elasticsearch的运行环境也是很好的一个选择，Docker可以快速的搭建我们所需要的环境，如果硬件条件允许，我觉得这个将是Elasticsearch环境搭建的首选。通过Docker的方式安装Elasticsearch前，请确保已经安装了Docker。

**安装步骤**

首先拉取Elasticsearch的Docker镜像：
```shell
docker pull docker.elastic.co/elasticsearch/elasticsearch:6.2.4
```
为了让我们能够方便的配置镜像中Elasticsearch的配置文件，我们可以用挂载配置文件的方式运行Elasticsearch镜像。

首先运行下载来的镜像：
```shell
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" --rm --name es docker.elastic.co/elasticsearch/elasticsearch:6.2.4
```
将镜像中的配置文件与Data文件夹拷贝到宿主主机：
```shell
docker cp 容器name:/usr/share/elasticsearch/config /opt/elasticsearch/config/
docker cp 容器name:/usr/share/elasticsearch/data /opt/elasticsearch/data/
```
在宿主主机相应目录中修改相关配置如Elasticsearch.yml。

用挂载的宿主主机中的配置文件运行Elasticsearch的Docker镜像：
```shell
docker run -p 9200:9200 -p 9300:9300 -d -e "discovery.type=single-node" --rm --name es -v /opt/elasticsearch/config:/usr/share/elasticsearch/config -v /opt/elasticsearch/data:/usr/share/elasticsearch/data docker.elastic.co/elasticsearch/elasticsearch:6.2.4
```
因为加了<code>-d</code>，所以当前运行模式是后台运行，不会有什么输出，若要停止这个镜像的运行，输入<code>docker ps</code>查看相应的信息，根据相关信息使用<code>docker stop</code>命令，停止Elasticsearch服务。在上面的例子中我们使用<code>docker stop es</code>来停止Elasticsearch服务。

#### 安装Elasticsearch常见错误
这一部分部分引用<code><a href="https://github.com/DimonHo/DH_Note/issues/3">这篇文章</a></code>，结合我遇到的实际问题做了些修改。

**问题1：**
```sehll
[2017-04-13T00:14:20,144][WARN ][o.e.b.JNANatives ] unable to install syscall filter:
java.lang.UnsupportedOperationException: seccomp unavailable: CONFIG_SECCOMP not compiled into kernel, CONFIG_SECCOMP and CONFIG_SECCOMP_FILTER are needed
```
- 原因：报了一大串错误，大家不必惊慌，其实只是一个警告，主要是因为你Linux版本过低造成的。一般如果VPS为OpenVZ构架的很有可能出现这个问题，因为主机的内核一般都很低而且你没法升级内核，Elasticsearch推荐内核版本为3.10以上。
- 解决方案：
	1. 重新安装新版本的Linux系统
	2. 警告不影响使用，可以忽略

**问题2：**
```shell
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
```
>**注意：如果你的VPS为OpenVZ构架，可以不用接着往下看了，因为你什么也做不了，所以买VPS推荐买KVM构架的，KVM构架更加独立，在你自己手上的控制权更多。**

这里报了若干个错误，我们一个一个来
```shell
max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
```
- 原因：无法创建本地文件问题，用户最大可创建文件数太小
- 解决方案：
	1. 切换到root用户，编辑limits.conf配置文件，运行：<code>vi /etc/security/limits.conf</code>添加如下内容(备注：* 代表Linux所有用户名称（比如 hadoop）保存、退出、重新登录才可生效。)：
```shell
* soft nofile 65536
* hard nofile 131072
* soft nproc 2048
* hard nproc 4096
```

```shell
max number of threads [1024] for user [es] is too low, increase to at least [2048]
```
- 原因：无法创建本地线程问题,用户最大可创建线程数太小
- 解决方案：
	1.切换到root用户，进入limits.d目录下，修改90-nproc.conf 配置文件。<code>vi /etc/security/limits.d/90-nproc.conf</code>找到如下内容：
<code>* soft nproc 1024</code>修改为：<code>* soft nproc 2048</code>

```sehll
max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]
```
- 原因：这个问题比较常见，原因是因为最大虚拟内存太小
	1. 解决方案：切换到root用户下，修改配置文件sysctl.conf，<code>vi /etc/sysctl.conf</code>添加下面配置：
<code>vm.max_map_count=655360</code>，并执行命令：<code>sysctl -p</code>，然后重新启动Elasticsearch，即可启动成功。

**问题3：**

启动后，如果只有本地可以访问，尝试修改配置文件 elasticsearch.yml中network.host(注意配置文件格式不是以 # 开头的要空一格， ： 后要空一格)为
<code>network.host: 0.0.0.0</code>，默认端口是 9200，注意：关闭防火墙 或者开放9200端口。

**问题4：**
```shell
Java HotSpot(TM) Client VM warning: INFO: os::commit_memory(0x74800000, 201326592, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (malloc) failed to allocate 201326592 bytes for committing reserved memory.
```
- 原因：这个错误是由于Elasticsearch尝试用默认配置的2G内存启动（Elasticsearch 5.X以后的版本），但你的机器实际内存小于2G导致的内存不足问题。
- 解决方案二选一即可：
	1. 升级你机器的内存。
	2. 修改Elasticsearch中jvm的配置，即修改<code>elasticsearch-6.2.4/config/</code>中的jvm.options文件。修改其中的<code>-Xms2g -Xmx2g</code>为<code>-Xms512m -Xmx512m</code>即可。注意这个参数可能需要根据具体情况具体设置，但有个原则就是<code>-Xms</code>与<code>-Xmx</code>需要相等。

#### 安装elasticsearch-head插件
elasticsearch-head是个用来与Elasticsearch互动的图形化界面插件，有了他你可以很方便的管理你的Elasticsearch，查看你的Elasticsearch状态或者测试你的查询语句。这个是他官方的<code><a href="https://github.com/mobz/elasticsearch-head">GitHub页面</a></code>。

**安装步骤：**
```shell
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
```
安装完成后用http://localhost:9100/ 打开即可。

>elasticsearch-head也推出了Chrome插件，**个人推荐直接安装Chrome的插件来使用elasticsearch-head**，非常非常方便。

#### 安装elasticsearch-analysis-ik插件
elasticsearch-analysis-ik是一个Elasticsearch的中文分词插件，支持自定义词库以及热更新，这个是他官方的<code><a href="https://github.com/medcl/elasticsearch-analysis-ik">GitHub页面</a></code>。

**安装步骤：**

方式一：

从<code><a href="https://github.com/medcl/elasticsearch-analysis-ik/releases">这里</a></code>下载你Elasticsearch相应版本的安装包，解压缩后放在<code>your-es-root/plugins/</code>中。

方式二：

使用Elasticsearch-plugin来安装，这个方式支持v5.5.1以后的版本。
```shell
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.3/elasticsearch-analysis-ik-6.2.3.zip
```
注意将6.2.3换成你自己Elasticsearch所用的版本，安装完成后重启Elasticsearch服务。

**如何使用：**

首先创建一个index：
```shell
curl -XPUT http://localhost:9200/index
```
然后创建mapping：
```shell
curl -XPOST http://localhost:9200/index/fulltext/_mapping -H 'Content-Type:application/json' -d'
{
        "properties": {
            "content": {
                "type": "text",
                "analyzer": "ik_max_word",
                "search_analyzer": "ik_max_word"
            }
        }
    
}'
```
之后就像往常一样，创建doc并且查询这个中文字段：
```shell
curl -XPOST http://localhost:9200/index/fulltext/1 -H 'Content-Type:application/json' -d'
{"content":"美国留给伊拉克的是个烂摊子吗"}
'
curl -XPOST http://localhost:9200/index/fulltext/2 -H 'Content-Type:application/json' -d'
{"content":"公安部：各地校车将享最高路权"}
'
curl -XPOST http://localhost:9200/index/fulltext/3 -H 'Content-Type:application/json' -d'
{"content":"中韩渔警冲突调查：韩警平均每天扣1艘中国渔船"}
'
curl -XPOST http://localhost:9200/index/fulltext/4 -H 'Content-Type:application/json' -d'
{"content":"中国驻洛杉矶领事馆遭亚裔男子枪击 嫌犯已自首"}
'
curl -XPOST http://localhost:9200/index/fulltext/_search  -H 'Content-Type:application/json' -d'
{
    "query" : { "match" : { "content" : "中国" }},
    "highlight" : {
        "pre_tags" : ["<tag1>", "<tag2>"],
        "post_tags" : ["</tag1>", "</tag2>"],
        "fields" : {
            "content" : {}
        }
    }
}
```
查询结果：
```json
{
    "took": 14,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 2,
        "hits": [
            {
                "_index": "index",
                "_type": "fulltext",
                "_id": "4",
                "_score": 2,
                "_source": {
                    "content": "中国驻洛杉矶领事馆遭亚裔男子枪击 嫌犯已自首"
                },
                "highlight": {
                    "content": [
                        "<tag1>中国</tag1>驻洛杉矶领事馆遭亚裔男子枪击 嫌犯已自首 "
                    ]
                }
            },
            {
                "_index": "index",
                "_type": "fulltext",
                "_id": "3",
                "_score": 2,
                "_source": {
                    "content": "中韩渔警冲突调查：韩警平均每天扣1艘中国渔船"
                },
                "highlight": {
                    "content": [
                        "均每天扣1艘<tag1>中国</tag1>渔船 "
                    ]
                }
            }
        ]
    }
}
```

#### 安装x-pack插件
x-pack是一个将各种插件集合起来的官方插件，这个插件集成了官方的Security（旧称 Shield），Alerting（通过 Watcher 实现），Monitoring（旧称 Marvel），Reporting，Graph，Machine Learning等。遗憾的是这个插件是付费的，如果只是自己学习用途，买个证书完全不合算。我们刚安装完Elasticsearch时，官方提供给我们一个试用licence，这是个全功能的证书，但是只能用30天。如果试用证书过期，官方另外提供了一种免费证书，类型为Basic，功能有限，具体可以看<code><a href="https://www.elastic.co/subscriptions/">这里</a></code>。x-pack我并不想详细讲，因为Basic证书连最基本的http认证都不提供，这表示如果将服务器暴露在外网环境任何人都可以随意操作我的Elasticsearch。最终我抛弃了x-pack并且通过Nginx的反向代理实现了最基本的http认证。

x-pack的介绍与安装步骤详细的信息可以看<code><a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/installing-xpack-es.html">这里</a></code>。

证书相关操作可以看<code><a href="https://www.elastic.co/guide/en/x-pack/6.2/license-management.html">这里</a></code>。

Nginx相关设置可以看<code><a href="https://www.jianshu.com/p/7ec26c13abbb">这里</a></code>

#### 资源列表
* <code><a href="https://www.elastic.co/guide/cn/elasticsearch/guide/cn/preface.html">Elasticsearch: 权威指南</a></code>， 这个是中文版的，容易看懂，但是有个问题就是版本有点老，最新版中已经移除type概念，这个指南中还存在这个概念，可以一看但更推荐看英文最新文档。
* <code><a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html">最新文档</a></code>，最新的英文文档，推荐阅读。






