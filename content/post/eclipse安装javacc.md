---
title: Eclipse安装JavaCC
tags:
  - java
  - 大学
  - 学习
  - 编译
  - 软件
id: 1507
categories:
  - 学习笔记
date: 2012-09-17 18:26:35
---

　　今年的编译原理课程上机实践是讲这个的。。要求用JavaCC来做一个简单的解释器。今天总算是有点时间找找文档先把这个安装了。安装过程很简单。。

　　1.希望你已经安装了Eclipse。。。

　　2.去[sourceforge](http://sourceforge.net/projects/eclipse-javacc/)该项目主页下载javaCC压缩包

　　3.下载完成后解压到你的Eclipse根目录。会提示覆盖plugins和features。直接覆盖即可。

　　4.测试一下是否成功。具体步骤就是，打开eclipse，新建一个空java项目。然后对着项目点击右键new->other->javaCC->javaCC template file。然后选择命名空间。包名。和文件名就可以了。这个地方的什么的是由你建立的java项目决定的。所以你可以先把java项目设置好，然后直接选就可以了。。

　　5.点击运行。选择java application。控制台输出　　
　　<pre class="lang:java decode:true " >Reading from standard input...　　
　　Enter an expression like "1+(2+3)*4;":</pre>

　　完成。。。

Update:其实吧。这里安装的是JavaCC的插件。但是已经可以进行开发了。可能你需要一个例子来开始。或者你不喜欢eclipse。那么去[JavaCC](http://javacc.java.net)的项目下载你喜欢的即可。我下的是javacc-5.0.zip 里面有例子。可以参考。