title: recon-ng开源信息探测框架
tags:
  - 互联网
  - 安全
  - 收藏
  - 软件
id: 2669
categories:
  - 我的翻译
date: 2013-02-02 06:52:28
---

作者：bystander
博客：[http://leaver.me](http://leaver.me)
微博：[http://t.qq.com/lazystander](http://t.qq.com/lazystander)
论坛：法客论坛

首发。转载什么的请注明出处。起码给我留个链接啥的嘛。

首先介绍一下。这个工具是国外刚刚发布的。主要用来渗透前的信息探测。使用类似Metasploit
主要有
Auxiliary：
这个模块查询主机信息泄漏页。进行hash反查，模糊名称精确，检查某个email是否密码泄漏，域名解析ip等
Contacts：
这个模块探测和某一公司有关的人员的信息，主要包括 LinkedIn 和Jigsaw 这两个模块。得到的信息可以被Auxiliary模块使用，如果和Social Engineer Toolkit(社会工程学工具集，这个工具已经发布了。是开源的。大家可以看看)，一起。效果强大。

Hosts：
这个用来获取站点子域名。。包括使用baidu。Google bing等。。效果相当强大。
Output：
这个模块用来创建输出报表
Pwnedlist：
这个模块不是得shell的。他可通过 Pwnedlist.com 提供的api，如果这个网站被入侵过。那么可以直接获得其他黑客泄漏的帐号密码。。（需要去 Pwnedlist.com 注册）

安装方法：
bt下直接
<pre class="lang:default decode:true">git clone https://LaNMaSteR53@bitbucket.org/LaNMaSteR53/recon-ng.git</pre>
然后有可能提示输入密码，好象是随便输一个用来保护版本控制。。我输的是toor。。
然后就安装好了。输入
<pre class="lang:default decode:true">cd recon-ng</pre>
然后
<pre class="lang:default decode:true">./recon-ng.py</pre>
首先查看有哪些模块。输入
<pre class="lang:default decode:true">modules</pre>
图一

[![]({{BASE_PATH}}/images/88f2dd5c24ad567a09c5b6162cd60d70b3fb781d.jpg)](http://leaverimage.b0.upaiyun.com/32339_o.jpg)

我以获取子域名为例，通过我前面的介绍你已经知道了hosts模块里的所有模块基本都是干这事的。我用里面的baidu模块来说明。你也可以使用bing等，，

输入命令
<pre class="lang:default decode:true">load hosts::baidu</pre>
图二

[![]({{BASE_PATH}}/images/f818e08de804ba8875667692fbdd580ad8114667.jpg)](http://leaverimage.b0.upaiyun.com/32340_o.jpg)

刚开始你可能不清楚这个模块的说明。那么继续输入info即可查看模块的详细说明
要开始使用。我们输入
<pre class="lang:default decode:true">options</pre>
和Metasploit很像把。可以查看要使用需要的配置。

图三

[![]({{BASE_PATH}}/images/7848e773ab3e4e3ad03f59ba7b5ac6704d333ce6.jpg)](http://leaverimage.b0.upaiyun.com/32341_o.jpg)

看表，会发现有三行。第一行是标题，第二行是域名设置，第三行是输出。这个current value也就是当前值已经为true。所以不用设置。req的意思是是否必须设置。我们输入
baidu.com就是你的目标了。
<pre class="lang:default decode:true">set domain baidu.com</pre>
就会从百度的结果里提取百度的子域名信息了。要开始。我们输入
<pre class="lang:default decode:true">run</pre>
图四

[![]({{BASE_PATH}}/images/125a841869478ff39dd63113e2e9427c3ae1cf81.jpg)](http://leaverimage.b0.upaiyun.com/32342_o.jpg)

更多希望大家发掘吧。我抛砖。求引玉。