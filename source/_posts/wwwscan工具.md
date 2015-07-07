title: WWWScan GUI版--WebScan
tags:
  - 原创
  - 安全
id: 333
categories:
  - 学习笔记
date: 2012-04-18 21:56:11
---

　　网上一直有个版本是Wscan Gui Beta6，首先感谢作者的无私奉献，写代码不容易啊。但是这个我不知道为什么在我的电脑上总是有一些错误。很多功能虽然加上了，但是其实对我有点多余。毕竟，我只是用这个扫描一下。于是，决定自己用C#做个GUI版，模仿实现一下。

　　然后我看了一下目录结构。如下图所知
[![](/images/1ab70fd66fde9efa08507a1d3930a4c4c9c4e53f.jpg)](http://leaverimage.b0.upaiyun.com/20741_o.jpg)
　　貌似作者只是简单写了。然后对应着调用扫描器。我想了下，可以通过修改文件名来实现，因为wwwscan默认只能识别cgi.list，那么我想的是当我选中一种扫描类型后。将对应的字典，比如asp.list改成cgi.list。当然为了保护原文件，复制。。然后调用。这样就不用有这么多exe了。。

　　第二个我想实现作者的检测网站的脚本类型的功能，想到了两种方法。一种是循环访问index.xxx文件，xxx对asp jsp等，然后判断http状态码，200的话就可以对应判断出来网站脚本了。但是这样测试了一会没成功，还是算了，，然后想了一种猥琐流的方法，就是直接访问首页。然后把源码下载下来，然后搜索".xxx"字符串，找到就行了，，当然这两种方法都是不完善的。。我也没想到什么完美的。。希望有人知道的话留言指教。

　　最后的目录就清爽多了
[![](/images/5a2532b3507acd9c4ddd6787249e87f588554c46.jpg)](http://leaverimage.b0.upaiyun.com/20742_o.jpg)

　　那个4p啊，，就是盲扫描了。。不知道网站脚本的时候采用。。。

　　界面基本完全模仿了作者的UI，进行了略微调整，希望作者不介意。
[![](/images/d099861e804d120c52b106989f63254908490533.jpg)](http://leaverimage.b0.upaiyun.com/20743_o.jpg)

　　程序需要.net framework 3.5。。win7默认都有。不用担心。xp用户可以下载环境后使用。毕竟是扫描工具，国内那些2b杀软会报毒，不放心的去世界杀毒网自己扫描吧。千万不要用来干坏事，遵守我国相关法律法规。

[downloadicon href=http://pan.baidu.com/share/link?shareid=83803&uk=1493685990]WebScan下载[/downloadicon]