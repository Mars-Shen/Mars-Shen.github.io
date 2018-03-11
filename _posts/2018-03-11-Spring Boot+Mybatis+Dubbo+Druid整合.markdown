---
layout:     post
title:      "Spring Boot/Mybatis/Dubbo/Druid整合"
subtitle:   "使用Spring Boot与Mybatis/Dubbo/Druid整合,并且记录过程中出现的问题"
date:       2018-03-10 12:00:00
author:     "Mars Shen"
header-img: "img/Header/coder-doom-dos-code-hd.jpg"
categories: [Spring]
tags: [Spring Boot,Mybatis,Dubbo,Druid]
---

<blockquote>记录自己项目中使用Spring Boot与Mybatis/Dubbo/Druid整合过程中出现的问题,以及相应的解决方法.</blockquote>

<h2 class="section-heading">Spring Boot部分</h2>
<p><b>·生成一个简单的Spring Boot项目</b></p>
<p>
	首先去Spring Boot的官网项目生成器生成一个简单项目:<code><a href="https://start.spring.io/">start.spring.io</a></code>
	<img src="{{ site.baseurl }}/img/SpringBoot/SpringBoot-1.png" width="100%" heigh="100%">
	点Generate Project下载并且导入到本地IDE, 这里用的是Intellij.
</p>
<p><b>·Spring Boot Pom文件修改默认继承关系及引用</b></p>
<p>
	默认Pom文件Spring Boot是用继承Pom的方式导入Spring Boot的必须组件的,那如果项目Pom中本来就已经有一个<code>parent</code>存在了,那应该怎么办呢? Spring Boot给出了自己的解决办法
	{% gist Mars-Shen/76d8ac5531637af2273d08789d460733 %}
	使用<code>dependencyManagement</code>代替<code>parent</code>完成Spring Boot必须组件的导入.
</p>
<p><b>·出现错误</b></p>
<p>
	项目中出现:
	{% gist Mars-Shen/e67667277d02ecbed560dd93cc9319fa %}
	这是由于项目中已经存在log4j的jar包,导致运行时出现这个<code>java.lang.IllegalArgumentException: LoggerFactory is not a Logback LoggerContext but Logback is on the classpath.</code>错误. 通过<code>mvn dependency:tree</code>命令可以看到具体冲突的文件
	<img src="{{ site.baseurl }}/img/SpringBoot/SpringBoot-2.png" width="100%" heigh="100%">
	解决这个问题很简单, 只需要在Spring Boot组件导入的时候, 将有冲突的组件排除<code>spring-boot-starter-logging</code>再导入即可.
	{% gist Mars-Shen/356322d855a6563b992534c196354775 %}
</p>

<h2 class="section-heading">Spring Boot与Dubbo整合</h2>
<p><b>·Spring Boot与Dubbo整合</b></p>
<p>
	Spring Boot与Dubbo整合相对比较简单, 首先在Pom文件中导入相应的jar包:
	{% gist Mars-Shen/a59c8039a5d9bf18a02f6c91974f40e5 %}
	修改application.properties中的配置, 添加dubbo相关属性:
	{% gist Mars-Shen/27e0f6ed4018e015cd0fae9762115938 %}
	最后在服务生产者类中使用<code>com.alibaba.dubbo.config.annotation.Service</code>中的<code>@service</code>即可暴露service
	{% gist Mars-Shen/3e7cc59aecc92981f9aa295372704d6c %}
</p>
<p><b>·Dubbo Admin的使用</b></p>
<p>
	Dubbo控制台可以很好的监控当前服务发布者与消费者的状态, 建议调试的时候安装使用.<code><a href="http://dubbo.io/books/dubbo-admin-book/">安装教程</a></code>
</p>

<h2 class="section-heading">Spring Boot与Mybatis/Druid整合</h2>
<p><b>·Spring Boot与Mybatis/Druid整合</b></p>
<p>
	首先在Pom文件中导入相应的jar包:
	{% gist Mars-Shen/edbc084e2a3a64d6cbe4267ae36ea566 %}
	修改application.properties中的配置, 使用Druid链接数据库:
	{% gist Mars-Shen/eec054eb70abbda24e802f2d476d1faf %}
	创建一个用DatasourceConfig类来配置Data source:
	{% gist Mars-Shen/c56baa254bfb0af5f9328c78e496593d %}
	在启动类中加入<code>@ServletComponentScan</code>和<code>@MapperScan("cn.example.manager.mapper")</code>:定义mapper所在的包
	{% gist Mars-Shen/0160ce95e15b9755aedfc8d9c6c96df2 %}
	至此, 整个整合基本就配置完成.
</p>