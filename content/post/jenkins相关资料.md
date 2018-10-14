---
title: jenkins相关资料
tags:
  - java
  - 工作
  - 软件
id: 3771
categories:
  - 学习笔记
date: 2014-09-21 18:26:48
---

# jenkins相关资料

jenkins的前身是hudson,是为了做持续集成测试而诞生的框架.

简单说就是把代码流配置上去,然后该框架就能根据设定的时间或其他方式不间断的执行测试用例.并给出报告.这样就可以随时掌控代码质量.支持执行shell命令.支持短信通知等等.

jenkins,首先从[http://jenkins-ci.org/官方下载war包,然后有两种方式可以本地部署,一种直接执行](http://jenkins-ci.org/%E5%AE%98%E6%96%B9%E4%B8%8B%E8%BD%BDwar%E5%8C%85,%E7%84%B6%E5%90%8E%E6%9C%89%E4%B8%A4%E7%A7%8D%E6%96%B9%E5%BC%8F%E5%8F%AF%E4%BB%A5%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2,%E4%B8%80%E7%A7%8D%E7%9B%B4%E6%8E%A5%E6%89%A7%E8%A1%8C)
`java -jar hudson.war`,然后在本地的8080端口访问就可以了.另一种是我们希望部署在容器上,那么可以部署到tomcat,jetty等等.直接拷贝war包到对应的目录即可.

部署成功后,可以新建job,然后配置svn流,配置build时间,配置一下build之前的动作,配置一下各种命令,执行完成后通知等等就可以了.

最近因为有个功能感觉通过开发jenkins插件的方式会更加方便,周末大量读了一些文档,能找到的都是helloworl.摸索了一下,已经写出一个小的demo了,后面改进一下,.就ok了.先简单把这个过程中收集的资料整理一下.基本都看过觉得还不错的,官方文档不给力.下周希望可以写完,然后分享出来.

1.  [jenkins入门文档](http://pan.baidu.com/s/1wBp06)
2.  [The hudson book](http://pan.baidu.com/s/1pJySAMn)
3.  [Writing-first-hudson-plugin](http://pan.baidu.com/s/1bn8e5sj)
4.  [Hudson插件开发简介](http://blog.csdn.net/littleatp2008/article/details/7001793)
5.  [Hudson插件开发入门体验 ](http://blog.csdn.net/yzhou86/article/details/6874144)
6.  [插件界面设计设计的各种tag介绍](https://jenkins-ci.org/maven-site/jenkins-core/jelly-taglib-ref.html)
7.  [官方插件开发](https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial#Plugintutorial-CreatingaNewPlugin)
8.  [UI开发jelly介绍](https://wiki.jenkins-ci.org/display/JENKINS/Basic+guide+to+Jelly+usage+in+Jenkins)
9.  [jenkins入门(itech)](http://pan.baidu.com/s/1qWuNags)