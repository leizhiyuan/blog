title: motan源码阅读-客户端服务引用
date: 2016-05-31 20:00:45
tags:
 - 技术
 - 学习
 - java
categories:
 - 学习笔记
---

一旦服务器启动,服务开始提供,并且在配置中心注册了(配置中心可以是本地的地址,也可以是zk,也可以是其他的实现),那么客户端就要开始调用了

[点击看大图](/images/service_ref.png)

![服务引用](/images/service_ref.png)


服务引用 RefererConfig.getRef()

先是获取集群支持(先忽略,主要是配置中心相关的)

configHandler.refer(interfaceClass, clusters, proxy)
开始获取接口代理

1.一旦知道接口名,Class.forName加载接口类,就开始通过proxy工厂来为服务端接口创建代理了

2.jdk的Proxy类,直接来创建代理.同时代理要传入RefererInvocationHandler
这个类可以看错是真正的stub,封装了rpc调用请求.当在客户端获取到服务接口的bean的时候,实际上调用过程被这个类拦截,进行封装发送rpc

1.当接口被调用的时候,这个拦截器险根据拦截到的请求构造一个rpc请求

2.这里就会存在一个策略.该调用哪个,以FailoverHaStrategy为例

1.选择一个服务提供方

1.如果是jvm服务,那么直接从本地的服务map中取出一个调用就行

2.如果是真正的远程服务,这时候就进入nettyClient部分了

把请求向netty的Channel中写就行了.服务端会从Channel中取进行处理,然后放回来.这样客户端就拿到结果了


