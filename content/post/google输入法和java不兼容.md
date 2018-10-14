---
title: Google输入法和Java不兼容
tags:
  - java
  - 工作
id: 3582
categories:
  - 学习笔记
date: 2014-03-29 12:13:25
---

一直发现自己写的Swing界面无法关闭，症状是点击了界面的关闭按钮后程序会卡住，然后点击无响应最后就挂掉了。

最开始以为是自己的资源没释放，但是没理由呀，应该在关闭的时候会自动释放，而且改了代码也没用。

今天再执行另一个Swing的时候，eclipse报错了
<pre class="lang:default decode:true">#
# A fatal error has been detected by the Java Runtime Environment:
#
#  EXCEPTION_ACCESS_VIOLATION (0xc0000005) at pc=0x6d986cc3, pid=700, tid=2280
#
# JRE version: Java(TM) SE Runtime Environment (7.0_45-b18) (build 1.7.0_45-b18)
# Java VM: Java HotSpot(TM) Client VM (24.45-b08 mixed mode, sharing windows-x86 )
# Problematic frame:
# C  [GOOGLEPINYIN2.IME+0x96cc3]
#
# Failed to write core dump. Minidumps are not enabled by default on client versions of Windows</pre>
看问题说是Google拼音输入法冲突，导致访问禁止，在PChunder里卸载掉javaw的Google拼音模块，javaw马上就关闭了，于是修改系统的默认输入法为其他输入法问题解决。

我了个去，纠结了我好长时间。我说为啥公司电脑和我的电脑都会关不了呢...