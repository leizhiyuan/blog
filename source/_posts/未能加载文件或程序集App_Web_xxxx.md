title: 未能加载文件或程序集“App_Web_xxxx”
tags:
  - C＃
  - 原创
  - 大学
  - 学习
  - 总结
  - 编程
id: 1262
categories:
  - 学习笔记
date: 2012-07-14 23:18:13
---

　　今天在用WCF写服务的时候，服务一直连不上，直接查看svc文件，发现如下错误
未能加载文件或程序集“'App_Web_****, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null",****是一个随机字符串。

解决方法中：

　　在web.config里配置成这样子：
<pre class="lang:c# decode:true"> &lt;compilation debug="true" batch="false"&gt;</pre>
　　就好了。中午和下午一直在搞javascript连接WCF的demo，总算是晚上搞定了，，还是老样子，，最大的体会就是很多东西就是看着简单，写起来会有各种各样的问题，比如这次，即使照着微软的官方文档来，也会有错误。动手才是王道，不管做什么。这几天忙完了，写篇文章出来。

　　最后分享一下微软官方的[WCF与Ajax开发实践系列课程](http://www.microsoft.com/china/msdn/events/webcasts/shared/webcast/Series/WCF_Ajax.aspx)，我只能说WCF这东西没有哪一本书比微软官方的技术培训讲的更好了，非常建议学习。

　　武汉最近下雨了，天气挺凉爽，过几天准备回家吧。。