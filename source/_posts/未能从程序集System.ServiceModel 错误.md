title: 未能从程序集“System.ServiceModel 错误
tags:
  - 大学
  - 学习
  - 开发
id: 1384
categories:
  - 文章收藏
date: 2012-08-20 02:36:44
---

　　今天在把wcf发布到远程服务器后。出现了这个错误。

　　确运行报告“未能从程序集“System.ServiceModel, Version=3.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089”中加载类型“System.ServiceModel.Activation.HttpModule”。”。

　　因为远程服务器是临时装的。肯定是少装了什么东西。。果断Google。直接输入下面的命令。安装注册一下asp.net4就可以了。

　　<pre class="lang:default decode:true " >c:\windows\microsoft.net\framework64\v4.0.30319\aspnet_regiis.exe -iru</pre>

　　中间的版本号应该是自己去目录看一看。

　　哦。我的是win server 2008的服务器。如果你也遇到了同样的问题。试试吧。