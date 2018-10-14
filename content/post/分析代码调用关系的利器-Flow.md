---
title: 分析代码调用关系的利器-Flow
date: 2017-04-08 15:10:36
tags:
  - java
  - 工作
categories:
  - 学习笔记
---

今天推荐一个不错的软件.是idea 的插件.名字是Flow, 官方称:A better way to understand your Java applications,原理就是通过 java-agent 修改字节码,配置了拦截器,然后真实地跑一个测试用例,或者启动一下项目,就会生成一个真实的调用关系.官方地址:http://findtheflow.io/

之前阅读源代码,对于抽象类,或者接口,静态阅读代码不太容易确定具体的调用类,因此阅读有一定的阻碍,当然 debug 也行..但是这个可以通过跑用例,或者简单的测试用例,理清调用关系,非常不错.
可以对代码结构有一个整体关系

## 安装

安装比较简单:https://plugins.jetbrains.com/plugin/8362?pr=idea 直接安装idea 这个插件,然后重新启动 idea, 安装完成后的效果.

![安装结果](/images/flow_install.png)


## 使用

使用更简单,直接点击上图中的按钮,开始跑一下,即可,如果启动成功.控制台会有显示.

![开始启动](/images/flow_start.png)

然后,会在本地开启7575的端口,来显示结果.

## 效果

![结果](/images/flow_common.png)


![结果明细](/images/flow_detail.png)

注意,在结果页里,可以和 idea 源码交互,对着方法点右键,可以直接定位到 idea 代码中的源代码,非常方便.


![跳转](/images/flow_jump.png)


## 其他

其他,就是 可以在配置里设置根据哪些类,这样一些工具类啥的可以直接忽略了.

![运行配置](/images/flow_conf.png)

使用了一下,还是不错的.但是这个有个问题,如果你的项目自定义了 classloader/ 或者使用了自定义的容易,这个由于没有 mvn 的 jar 包,可能会报错,类找不到.暂时没有好的办法.但是阅读开源代码基本没有问题了.

