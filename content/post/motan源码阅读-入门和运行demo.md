---
title: motan源码阅读-入门和运行demo
date: 2016-05-29 15:13:48
tags:
 - 技术
 - 学习
 - java
categories:
 - 学习笔记
---

工作中一直在使用rpc,但是只是对简单的原理比较熟悉.最近看到有motan的一个介绍,代码拉下来看了看,除了测试用例比较少之外.其他还是不错的,和阿里的rpc框架比起来,还是弱了一些,好处就是方便用来学习.
[motan](https://github.com/weibocom/motan) 是weibo的一个rpc框架,据说已经在线上使用了.

在学习rpc框架之前,建议看一个hello world级别的文章[RPC框架几行代码就够了](http://javatar.iteye.com/blog/1123915),写的非常好,看完基本就知道rpc的核心了.

[Remote Procedure Calls](https://www.cs.rutgers.edu/~pxk/417/notes/08-rpc.html)中最关键的那个图,就能说明了.

![rpc的flow](/images/rpc-flow.png)

本地client调用本地client stub,stub对消息进行封装,通过socket发送,服务端的server stub接收到,然后解包,将里面传递的方法名,方法参数.等等信息,识别出来,调用服务端对应的服务,然后得到结果后,又通过socket返回,本地client又进行解包.就行了.


这里面会涉及到,封装,封装就是吧对象序列化,这样才能在网络中传递.


而生产环境的rpc框架需要考虑的有:

stub怎么生成,序列化怎么最高效,如何统一不同机器之前的调用,(大小端的机器等),如何识别该调用哪个机器,负载均衡.socket通信.等等.

先跑个demo熟悉一下.

下载motan源码,导入ide,然后先启动服务端,MotanApiExportDemo,这个类,然后控制台会打出服务已经启动.然后运行MotanApiClientDemo,会发现一个控制台打出motan,服务端打出hello motan.就说明跑起来了.

如果控制台日志没有.修改对应resources下面的log4j.properties文件.首行添加`log4j.rootLogger=debug,stdout` ,会设置默认日志级别为debug,并且在控制台输出.
或者直接fork[我这个](https://github.com/leizhiyuan/motan)

后面会逐步分析,希望坚持下来.


