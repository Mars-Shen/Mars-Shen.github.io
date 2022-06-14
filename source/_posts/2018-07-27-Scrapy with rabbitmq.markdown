---
layout: post
title: '如何使用RabbitMQ控制Scrapy爬虫'
subtitle: ''
author: Mars Shen
multilingual: false
tags:
  - 原创
  - 互联网技术
  - 淘秀网
  - scrapy
  - python
abbrlink: 39fb172e
date: 2018-07-27 12:00:00
updated: 2018-07-27 12:00:00
alias: /2018/07/27/Scrapy-with-rabbitmq/index.html
---
# 如何使用RabbitMQ控制Scrapy爬虫

>本文将介绍如何使用RabbitMQ整合Scrapy来控制Scrapy爬虫进行目标网页内容爬取。我们假设你已经使用过Scrapy和RabbitMQ或者对其有一定了解。

### 为什么要使用RabbitMQ来控制爬虫？

Scrapy爬虫其实有自己的一套生产环境部署控制系统<a href="https://github.com/scrapy/scrapyd">Scrapyd</a>，这是一个开源的项目，他给Scrapy提供了服务器端的HTTP API，使其拥有运行与监控Scrapy爬虫的能力，使用Scrapyd需要将我们的爬虫部署至Scrapyd服务器。

相比Scrapyd，RabbitMQ则多了一个队列的特性，同时也能监控与控制爬虫的，并且不需要将爬虫部署到特定的服务器，随时运行，同时与队列与我们整个项目的整合也更加平滑自如。目前<a href="https://www.marsshen.com/posts/26b6aa29/">淘秀网</a>爬虫端就是使用整合RabbitMQ这种解决方案来自动化控制与监控Scrapy爬虫。

### 如何整合RabbitMQ与Scrapy爬虫？

RabbitMQ在Python端的客户端实现是叫做<a href="https://pika.readthedocs.io/en/latest/intro.html">Pika</a>，这个RabbitMQ客户端提供了与别的RabbitMQ客户端大致相同的功能，如连接服务端，服务端连接管理，交换器管理，队列管理等等。我们将在我们的Scrapy中使用Pika来对RabbitMQ进行整合。

首先我们在<code>scrapy.cfg</code>同级目录下创建python运行文件<code>begin.py</code>，这个文件用于写我们的运行爬虫已经连接RabbitMQ相应的代码。
<!-- more -->
#### 连接RabbitMQ服务端部分

```python
while(True):
    try:
        # 创建连接认证条件
        pika_credentials = pika.credentials.PlainCredentials(rabbit_user, rabbit_pass)
        # 创建一个Blocking的连接
        connection = pika.BlockingConnection(pika.ConnectionParameters(
            rabbit_host, port=rabbit_port, credentials=pika_credentials, connection_attempts=10, socket_timeout=20,
            heartbeat=360))
        channel = connection.channel()
        # 声明交换器，这里应该和你消息生产者端保持一致设置
        channel.exchange_declare(exchange=rabbit_exchange, exchange_type='topic', durable=True, auto_delete=False)
        # 声明队列，这里应该和你消息生产者端保持一致设置
        channel.queue_declare(queue=rabbit_queue, durable=True, exclusive=False, auto_delete=False)
        # 绑定操作
        channel.queue_bind(exchange=rabbit_exchange,
                           queue=rabbit_queue,
                           routing_key=rabbit_queue)
        # 类似权重，按能力分发，如果有一个消息，就不在给你发，控制单个蜘蛛消费数量
        channel.basic_qos(prefetch_count=1)  
        channel.basic_consume(  # 消费消息
            callback,           # 如果收到消息，就调用Callback
                                # 这里的Callback函数我们下文将会提到，其实就是运行我们的Scrapy蜘蛛语句
            queue=rabbit_queue, # 队列
            # no_ack=True       # 一般不写，处理完接收处理结果。宕机则发给其他消费者
        )

        logger.info(' [*] Waiting for messages. To exit press CTRL+C')
        # 开启RabbitMQ接收模式，这代码会阻塞运行，直到爬虫完成任务，才会继续
        channel.start_consuming()
    # 对一些连接错误进行处理，将继续执行循环，从而完成重连
    except pika.exceptions.ConnectionClosed:
        # Uncomment this to make the example not attempt recovery
        # from server-initiated connection closure, including
        # when the node is stopped cleanly
        #
        # break
        continue
    # Do not recover on channel errors
    except pika.exceptions.AMQPChannelError as err:
        print("Caught a channel error: {}, stopping...".format(err))
        break
    # Recover on all other connection errors
    except pika.exceptions.AMQPConnectionError:
        print("Connection was closed, retrying...")
        continue
```
首先我们需要创建一个连接至RabbitMQ服务端的连接
```python
 # 创建一个Blocking的连接
connection = pika.BlockingConnection(pika.ConnectionParameters(
    rabbit_host, port=rabbit_port, credentials=pika_credentials, connection_attempts=10, socket_timeout=20,
    heartbeat=360))
```
这句话支持一些RabbitMQ队列连接相关的参数设置，如验证信息，重试次数，超时时间，心跳间隔等等，具体参数列表可以查看官方文档<a href="https://pika.readthedocs.io/en/0.12.0/modules/parameters.html">关于参数的介绍</a>。

然后就是相关的队列设置，交换器设置。
```python
channel = connection.channel()
        # 声明交换器，这里应该和你消息生产者端保持一致设置
        channel.exchange_declare(exchange=rabbit_exchange, exchange_type='topic', durable=True, auto_delete=False)
        # 声明队列，这里应该和你消息生产者端保持一致设置
        channel.queue_declare(queue=rabbit_queue, durable=True, exclusive=False, auto_delete=False)
        # 绑定操作
        channel.queue_bind(exchange=rabbit_exchange,
                           queue=rabbit_queue,
                           routing_key=rabbit_queue)
        # 类似权重，按能力分发，如果有一个消息，就不在给你发，控制单个蜘蛛消费数量
        channel.basic_qos(prefetch_count=1)  
        channel.basic_consume(  # 消费消息
            callback,           # 如果收到消息，就调用Callback
                                # 这里的Callback函数我们下文将会提到，其实就是运行我们的Scrapy蜘蛛语句
            queue=rabbit_queue, # 队列
            # no_ack=True       # 一般不写，处理完接收处理结果。宕机则发给其他消费者
        )
```
使用下面这句话开启我们的RabbitMQ Python客户端，之后便可以连接上RabbitMQ服务端
```python
# 开启RabbitMQ接收模式，这代码会阻塞运行，直到爬虫完成任务，才会继续
channel.start_consuming()
```
最后我们使用<code>while</code>语句，处理可能出现的连接异常，使我们的代码可以在出现连接异常的情况下，自动重新连接运行。

#### 如何运行我们的Scrapy爬虫？
上文我们提到了当有消息分配给我们这个客户端的时候，代码会触发<code>callback函数</code>，很明显我们需要在<code>callback函数</code>中运行我们的Scrapy蜘蛛。接下来我们看看如何写这个<code>callback函数</code>。
```python
#!/usr/bin/python
# !/usr/bin/env python
# -*- coding: utf-8 -*-
import logging

import pika
from crochet import setup
from scrapy.crawler import CrawlerRunner
from scrapy.utils.log import configure_logging
from scrapy.utils.project import get_project_settings
# 导入我们自己写的蜘蛛
from mars.spiders.myspider import Spider

setup()

settings = get_project_settings()

def callback(used_channel, basic_deliver, properties, body):
    # 获取到消息队列中的消息
    decodebody = bytes.decode(body)
    logger.info(" [x] Received %r" % decodebody)
    try:
        run_spider(key_word_arg=decodebody, used_channel=used_channel, delivery_tag=basic_deliver.delivery_tag)
    except Exception as error:
        logger.error(error)
        # 告诉生产者，消息未处理完成
        channel.basic_reject(delivery_tag=basic_deliver.delivery_tag)  


def run_spider(key_word_arg, used_channel, delivery_tag):
    # 使用CrawlerRunner运行蜘蛛
    crawler = CrawlerRunner(settings)
    # 运行我们自己写的蜘蛛
    crawler.crawl(Spider, key_word_arg=key_word_arg, channel=used_channel, delivery_tag=delivery_tag)
```
官方文档中，运行Scrapy蜘蛛有两种方式，一种叫做<code>CrawlerProcess</code>，另外一种叫做<code>CrawlerRunner</code>，我们在这里无法使用<code>CrawlerProcess</code>运行蜘蛛，因为当蜘蛛完成一次爬行后，接收到第二个消息，准备再次爬行时，使用<code>CrawlerProcess</code>会报<code>twisted.internet.error.ReactorNotRestartable</code>错误，导致蜘蛛无法再运行。而使用<code>CrawlerRunner</code>将完美解决这个问题。

这个解决方案中关键的一步是使用<code>from crochet import setup</code>导入<code>setup()</code>，并将<code>setup()</code>置顶放置。解决方法详细内容参见<a href="https://stackoverflow.com/a/47581199/3189161">这里</a>。

最后运行蜘蛛，这里可以通过传参，传入一些自定义参数，像下面我代码中<code>channel=used_channel</code>，传入channel的目的是为了让我们有能力在蜘蛛中向RabbitMQ客户端实现消息确认。这些都不是强制的，关键看你自己的蜘蛛如何实现。
```python
# 运行我们自己写的蜘蛛
crawler.crawl(Spider, key_word_arg=key_word_arg, channel=used_channel, delivery_tag=delivery_tag)
```

#### 如何运行整个文件
我们只需要像执行普通python文件一样执行这个<code>begin.py</code>文件即可，程序会自动连接至RabbitMQ服务端，自动获取消息，自动执行，当消费完一个消息后，自动获取下个消息进行消费。

### 完整版代码
```python
#!/usr/bin/python
# !/usr/bin/env python
# -*- coding: utf-8 -*-
import logging

import pika
from crochet import setup
from scrapy.crawler import CrawlerRunner
from scrapy.utils.log import configure_logging
from scrapy.utils.project import get_project_settings
# 导入我们自己写的蜘蛛
from mars.spiders.myspider import Spider

setup()
# 获取setting.py文件
settings = get_project_settings()

logger = logging.getLogger('begin.py')

configure_logging(settings)
logger.info(' [*] Starting begin.py...')
rabbit_host = settings.get("RABBITMQ_HOST")
rabbit_port = settings.get("RABBITMQ_PORT")
rabbit_user = settings.get("RABBITMQ_USERNAME")
rabbit_pass = settings.get("RABBITMQ_PASSWORD")
rabbit_exchange = settings.get("MY_EXCHANGE")
rabbit_queue = settings.get("MY_SCRAPY_QUEUE")


def callback(used_channel, basic_deliver, properties, body):
    # 获取到消息队列中的消息
    decodebody = bytes.decode(body)
    logger.info(" [x] Received %r" % decodebody)
    try:
        run_spider(key_word_arg=decodebody, used_channel=used_channel, delivery_tag=basic_deliver.delivery_tag)
    except Exception as error:
        logger.error(error)
        # 告诉生产者，消息未处理完成
        channel.basic_reject(delivery_tag=basic_deliver.delivery_tag)  


def run_spider(key_word_arg, used_channel, delivery_tag):
    # 使用CrawlerRunner运行蜘蛛
    crawler = CrawlerRunner(settings)
    # 运行我们自己写的蜘蛛
    crawler.crawl(Spider, key_word_arg=key_word_arg, channel=used_channel, delivery_tag=delivery_tag)

while(True):
    try:
        # 创建连接认证条件
        pika_credentials = pika.credentials.PlainCredentials(rabbit_user, rabbit_pass)
        # 创建一个Blocking的连接
        connection = pika.BlockingConnection(pika.ConnectionParameters(
            rabbit_host, port=rabbit_port, credentials=pika_credentials, connection_attempts=10, socket_timeout=20,
            heartbeat=360))
        channel = connection.channel()
        # 声明交换器，这里应该和你消息生产者端保持一致设置
        channel.exchange_declare(exchange=rabbit_exchange, exchange_type='topic', durable=True, auto_delete=False)
        # 声明队列，这里应该和你消息生产者端保持一致设置
        channel.queue_declare(queue=rabbit_queue, durable=True, exclusive=False, auto_delete=False)
        # 绑定操作
        channel.queue_bind(exchange=rabbit_exchange,
                           queue=rabbit_queue,
                           routing_key=rabbit_queue)
        # 类似权重，按能力分发，如果有一个消息，就不在给你发，控制单个蜘蛛消费数量
        channel.basic_qos(prefetch_count=1)  
        channel.basic_consume(  # 消费消息
            callback,           # 如果收到消息，就调用Callback
                                # 这里的Callback函数我们下文将会提到，其实就是运行我们的Scrapy蜘蛛语句
            queue=rabbit_queue, # 队列
            # no_ack=True       # 一般不写，处理完接收处理结果。宕机则发给其他消费者
        )

        logger.info(' [*] Waiting for messages. To exit press CTRL+C')
        # 开启RabbitMQ接收模式，这代码会阻塞运行，直到爬虫完成任务，才会继续
        channel.start_consuming()
    # 对一些连接错误进行处理，将继续执行循环，从而完成重连
    except pika.exceptions.ConnectionClosed:
        # Uncomment this to make the example not attempt recovery
        # from server-initiated connection closure, including
        # when the node is stopped cleanly
        #
        # break
        continue
    # Do not recover on channel errors
    except pika.exceptions.AMQPChannelError as err:
        print("Caught a channel error: {}, stopping...".format(err))
        break
    # Recover on all other connection errors
    except pika.exceptions.AMQPConnectionError:
        print("Connection was closed, retrying...")
        continue
```

完

