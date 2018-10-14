---
title: WCF读书笔记(4)
tags:
  - WCF
  - 大学
  - 学习
  - 开发
  - 武汉
id: 1444
categories:
  - 学习笔记
date: 2012-09-09 09:06:33
---

**三种供客户端和服务端控制通信的契约介绍：**
1.服务契约描述了由特定服务端点所公开的操作，每一种操作，通过参数和返回值定义请求和响应消息的格式。
2.数据契约描述了复杂类型如何被串行化为消息的一部分，数据契约是服务契约中优先用来包含复杂类型的方式。
3.消息契约提供对某个soap消息格式的控制，包括支持定制消息标题和数据契约所描述的单个消息体元素。

**消息参数（Message Parameter）**
对于

<pre class="lang:c# decode:true " >[OperationContract]
string MyOp(string s);
</pre> 
若客户端传入的参数为“Hello”，则生成的请求消息体中标记为<s> Hello</s>,而响应返回的消息体则被标记为<NewOpResult>返回内容</NewOpResult>,可以通过

<pre class="lang:default decode:true " >[OperationContract]
[return:MessageParameter(Name="ResponseString"]
string MyOp([MessageParameter(Name="RequestString")] string s);</pre> 
来定制消息中的标签。

**绑定元素**
实际上，每个绑定元素都会被映射到一个信道上，这样，绑定元素和信道在这个意义上可以互换。

**执行上下文（OperationContext）**
是System.ServiceModel命名空间的一种类型，他为服务请求提供了对执行上下文的访问，OperationContext.Current为请求在生命周期期间提供了对上下文的访问。

**WCF的实例化模式**
实例化控制模式服务对象被分配给请求的方式，一旦服务主机已经建立而且为每个端点创建了信道监听器，对各终端的请求已经由适当的服务对象所执行，则这些对象是基于服务类型的实例化模式的。他们是InstanceContextMode的一个枚举。
1.PerCall 服务对象为每一个对服务的调用所创建。
2.PerSession 对每一个客户端创建一个。默认是这样
3.Single 创建单一的服务对象。并由所有客户端的调用使用。
尽可能使用PerCall，大规模的部署避免PerSession并发。

**WCF的四种会话**
应用会话，传输会话，可靠会话，安全会话。

**信道发生器取得SessionId**

<pre class="lang:c# decode:true " >ChannelFactory&lt;IService&gt; factory=new ChannelFactory&lt;IService&gt;("Service");
IService proxy=factory.CreateChannel();
IContextChannel obj=proxy as IContextChannel;
string s=obj.SessionId;</pre> 