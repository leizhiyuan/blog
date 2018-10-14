---
title: jdk8_cannot_access_class_file
date: 2017-03-31 13:48:50
tags:
  - java
  - 工作
categories:
  - 学习笔记
---

之前有个项目用 jdk6跑运行正常,用 jdk8跑的时候,会报`java cannot access ....class file ...as class file not found though it exists.`
虽然可以通过加上报错的类到依赖里解决.但是一直没想明白,为啥 jdk6下没报错.


最近再次遇到,于是想一次性搞清楚.搜了一下,看 so 上有这么个说法.大意就是以前,如果 A 依赖 B,B 实现了 C 接口,编译的时候, 用 jdk8编译的时候, C 必须在 classpath 中,
http://stackoverflow.com/questions/40255718/compiling-with-jdk-1-8-java-cannot-access-class-file-class-file-not-found

给出了一个 bug 连接,但是这里跟我们的问题有差异,不过这个点提醒了我.于是我搜索了一下 jdk8的relase note

http://www.oracle.com/technetwork/java/javase/8-compatibility-guide-2156366.html

注意观看这一段:

>Area: Tools / javac 
>Synopsis
>Interfaces need to be present when compiling against their implementations

好了.也就是说还是乖乖加依赖.但是清楚了原因了