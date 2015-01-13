title: WCF读书笔记(1)
tags:
  - WCF
  - 大学
  - 学习
  - 开发
  - 总结
id: 1419
categories:
  - 学习笔记
date: 2012-09-05 19:29:14
---

**信道的分类：Transport Channel 信道**

　　1.Message Encording Channel　　
　　2.Protocol Channel

**信道管理器（Channel Manager）,信道管理器用于信道栈的创建和生命周期的管理**

　　1.ChannelListener　　
　　2.ChannelFactory

**WCF服务调用的两种典型方式**

　　1.是借助代码生成工具svcUtil.exe导入元数据生成客户端代码和配置，添加服务引用采用的就是这种方式，工具会创建一个继承自Client<TChannel>的服务代理类型。

　　2.是通过ChannelFactory直接创建服务代理对象进行服务调用。

　　如果客户端已经进行了终结点的配置

　　那么通过信道工厂进行调用的代码大致如下：　 

<pre class="lang:c# decode:true " >using(Channel&lt;IService&gt; channelFactory=new ChannelFactory&lt;IService&gt;("Service"))　　
{　　
　　IService ise=channelFactory.CreateChannel();　　
　　using(ise as IDisposable)　　
　　{
　    　ise.MethodName();
　　}　
}</pre> 

　　WCF处理的是跨应用程序域，跨机器，跨网络的通信，所以WCF大多数时间进行网络传俗这样的IO操作，IO绑定的操作是采用异步编程（APM【Asynchronous Programming Model】）

**消息交换模式MEP（Message Exchange Pattern）**

　　MEP定义了参与者进行消息交换的模板，代表一系列的模板，他们定了消息的发送者和接收者，相互进行消息传递的次序，比较典型的三种

　　1.Datagram 数据包模式，嘴尖的SEND/FORGET模式也叫One-Way模式，基于从一个源到另一个或多个目的地的单向消息传输，并不期待回复

　　消息报的发送可以分成三个模式，分别是单目的地模式，多投模式，广播模式。 依次接受方更强大。。

　　2.Request/Reply模式

　　使用最多的一种模式，请求期待回复。采用同步通信方式，但也可用于异步通信

　　3.Duplex 双工模式

　　双方可以互发消息，实现服务器回调客户端。订阅/发布是其中一种典型的实例，TCP可以提供原生的双工通信，WCF通过WSDualHttpBinding实现了基于Http的双工通信，实际上是采用两个HTTP通道实现