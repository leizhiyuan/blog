title: Maven提示缺少tools.jar
tags:
  - 学习
  - 笔记
id: 3236
categories:
  - 学习笔记
date: 2013-09-19 13:24:26
---

记录一下。

这两天在熟悉Maven，长见识了.后续可能的话会写上一两篇，今天配置的时候提示tools.jar文件。于是使用everything搜了一下，本机的jdk目录还真没有，最后搜了一下，发现是安装jdk时候的问题，具体就是因为安装jdk的时候，后面被让继续安装jre，这个时候，我为了方便，将jre安装在了jdk的目录里，结果导致jre会覆盖到jdk的这两个文件。同时还会覆盖dt.jar这个包，于是，就没了。。这个问题略隐晦了..

&nbsp;

重新安装了jdk，将jdk和jre分开目录，然后设置一下jdk的lib目录到classpath就可以了，问题解决。