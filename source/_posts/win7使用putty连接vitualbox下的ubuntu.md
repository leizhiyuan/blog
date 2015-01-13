title: Win7使用Putty连接VitualBox下的Ubuntu
tags:
  - windows
id: 403
categories:
  - 学习笔记
date: 2012-04-26 22:40:27
---

推荐连接方式选择Host-only Adapter（主机模式）。设置方法是打开vitualbox，然后选中虚拟机，点击设置，找到网络，然后如下图选择

[![]({{BASE_PATH}}/images/6f75b3ea6509800b51958f720d2e3173390bc0a1.jpg "桥接设置")](http://leaverimage.b0.upaiyun.com/20944_o.jpg)

设置完成后，启动ubuntu，然后执行
<pre lang="php">ifconfig -a</pre>
找到下面这行，可以看到虚拟机分配到的ip地址为192.168.56.101

[![]({{BASE_PATH}}/images/a57df08f6538afb4deebab8a4e08564bda4528e0.jpg "IP地址")](http://leaverimage.b0.upaiyun.com/20945_o.jpg)

然后呢，可以在win7的cmd下 ping 192.168.56.101，看看可不可以ping通，

[![]({{BASE_PATH}}/images/b38276de90d54e82042a95ae97e40e658753d1d5.jpg "ping")](http://leaverimage.b0.upaiyun.com/20948_o.jpg)

有返回所以是通的，

然后在ubuntu下需要执行
<pre lang="php">sudo apt-get install openssh-server</pre>
Ubuntu缺省安装了openssh-client（用于ubuntu连接其他服务器）,所以在这里就不安装了，只安装server，用于其它电脑连接ubuntu，如果你的系统没有安装的话，再用apt-get安装上即可。

然后确认sshserver是否启动了：
<pre lang="php">ps -e |grep ssh</pre>
如果只有ssh-agent行那ssh-server还没有启动，需要执行
<pre lang="php">service sshd start</pre>
，启动ssh服务器
如果看到sshd那说明ssh-server已经启动了。

然后下载putty，推荐去[官方下载](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)，下载完成后发现是单文件，直接执行即可。

输入ubuntu的ip点击open即可

[![]({{BASE_PATH}}/images/9901f47b9a70d8c61d8ea28aeb41985e4b92be9d.jpg "登录设置")](http://leaverimage.b0.upaiyun.com/20950_o.jpg)

登录上以后执行命令会发现有乱码，鼠标右键点击putty窗口的标题栏，选择，"Change Settings"，"Translation"，在"Received data assumed to be in which character set"的下拉菜单里选择"UTF-8"。如下图

[![]({{BASE_PATH}}/images/6d08c4a6755cd31c3dc560f681162e46c797c02d.png "utf-8")](http://leaverimage.b0.upaiyun.com/20951_o.png)

这样，下次又得重复同样的工作，为了保持配置，继续在上图选择左边的session

[![]({{BASE_PATH}}/images/d0111c6b9386937d97e77046d84dbd510a062982.jpg "session")](http://leaverimage.b0.upaiyun.com/20952_o.jpg)

在saved session输入个名字，save即可，下次直接连这个就可以了

参考：[http://www.linuxidc.com/Linux/2011-12/49325.htm](http://www.linuxidc.com/Linux/2011-12/49325.htm)

[http://spark10000.blog.51cto.com/955100/547211](http://spark10000.blog.51cto.com/955100/547211)

至于putty的使用不在本文的计划范围内，以后有机会再写吧。

每次遇到问题就体会到网络的信息实在太杂了。掌握搜索技术和对数据的快速筛选很重要。

&nbsp;