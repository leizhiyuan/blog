---
title: 'javOSize:新一代java分析工具'
date: 2015-07-04 16:18:57
tags:
  - 开发
  - 工作
categories: 学习笔记
---

## 介绍 ##
最近看到这么个工具-javOSize .[官网地址](http://www.javosize.com/),去官网看了下.发现介绍很有意思,叫做 the missing sugar for your Java cup.(你的咖啡杯中缺少的那颗糖).于是感受一下这颗糖到底甜不甜

## 安装 ##
安装非常简单,从这里[下载](http://www.javosize.com/download.html),其实完成之后就是一个jar包.非常简单,不用配置啥的.

## 简单使用 ##
使用方法就是从先附加到一个java进程上,在linux,上我直接执行
`ps aux|grep java `
 就能看到了.不废话,得到java进程的pid之后,运行 `java -jar javosize-1.0.9.jar pid` pid就是pid的号了. 然后会看到如下图的界面![启动页面](/images/javosize_start_up.jpg),表示已经附加成功了.先看看都有啥.执行`ls`.看到有这么几个命令.我比较单纯,刚开始以为是可执行文件.结果丢人了.看了下官方文档,才知道是目录.进入对应的目录.然后执行```ls```就能看到对应的信息了.ls简直不能再万能..改目录支持的所有命令通过在对应的目录执行`help`来查看.个人尝试觉得好的功能有. 
![所有功能目录](/images/javosize_command_ls.jpg)

1. 动态修改类
 进入CLASSES目录,然后通过执行
 `ls|grep xxx `
 来找到需要修改的类,然后通过vi 就可以直接编辑.编辑完成后,直接保存就会动态替换.文档里说是支持jdk1.7以上,对1.6的支持估计是不行的.没有测试.
 ![修改类demo](/images/javosize_command_class_modify.jpg)
2. 无须重启开启jmx
 直接挂载之后,执行`cd REPOSITORY` 然后执行就好了.
`exec START_JMX_SERVER 6666`
3. 拦截任意代码
 可以动态添加类似Spring中的aop方法.用于打印一些调用日志,排查线上问题.进入INTERCEPTOR目录.然后通过如下的方法给某个类的某个方法添加拦截器.
 `create TickServlet begin mypackage.Hello doGet System.out.println("Servlet invoked");`
4. 检测内存泄漏
进入 REPOSITORY 目录,执行 ` exec  TOP_FAT_STATIC_VARIABLES  5 com.apache.* `会取出某个包下面占用大小最大的5个类.会很方便找到.
5.  自动检测性能问题
PROBLEMS 目录专业解决这种问题.进入目录后.执行ls命令.会看到这样的场景
    >* Concurrency
    >    Deadlocked: false
    >* Memory
    >    High GC (>2%): false
  
    这里目前只检测了死锁和频繁GC.已经很有用了.至于效果还有待观察.

6. 无需重启开启GC 日志
  和开启jmx一样,进入JMX目录后,执行
    `exec java.lang:type=Memory.setVerbose(true);`
   就开启了.

## 更多文档 ##
1. [官方文档](http://www.javosize.com/gettingStarted.html)
