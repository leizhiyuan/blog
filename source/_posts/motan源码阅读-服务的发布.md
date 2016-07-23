title: motan源码阅读-服务的发布
date: 2016-05-30 20:42:06
tags:
 - 技术
 - 学习
 - java
categories:
 - 学习笔记
---

这一篇继续从这个demo开始,分析一下这个服务是怎么发布出去的.关键的代码从`motanDemoService.export();`开始.

一图胜千言.

[点击看大图](/images/serviceConfig_export.png)

![服务发布](/images/serviceConfig_export.png)

服务发布ServiceConfig.export()

1.加载有的配置中心url列表/新建

2.doExport(ProtocolConfig,port,registryURLs) //配置中心地址列表


2.1导出的时候,会先判断是否存在.其实就是根据协议名,ip,接口,参数来生成一个唯一key.

2.2ConfigHandler.export(Class<T> interfaceClass, T ref, List<URL> registryUrls) //接口.实现.配置中心url列表


2.2.1.根据协议名创建协议,这里ProtocolFilterDecorator

2.2.2.根据接口,实现类,serviceUrl,构造一个Provider,用来提供服务

2.2.3.使用协议进行导出Provider,  export(Provider<T> provider, URL url)

2.2.3.1创建一个Exporter

2.2.3.1.1.创建的时候会将服务提供方Provider和url有个映射关系,这样当一个url请求过来的时候,就知道改调用谁了.ProviderMessageRouter,讲一个请求路由注册到server上,同时包装了一个心跳包

2.2.3.2进行导出 导出就是一个服务器打开的过程/server.open();

2.2.3.2.1进入nettyServer初始化,主要就是添加handler,编码解码.和一个rpc处理的
相当于一个请求过来的时候,先进行解码,然后调用业务处理handler进行处理,处理完成后,进行编码,然后返回给客户端

服务器启动后,相当于这个服务就发布了

2.2.4.注册register(registryUrls, serviceUrl) //这一步就是将serviceUrl,向对应的jvm/rpc服务中心注册url,本地注册就是LocalRegistryService类里一个map..zk的.就是向zk写node.等等

