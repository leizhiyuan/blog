---
title: linux的CPU负载均值
tags:
  - linux
  - 上海
  - 工作
id: 3542
categories:
  - 学习笔记
date: 2014-03-08 17:19:30
---

当运行在Linux上的程序有问题之后，我们通常要看一下当前CPU和内存的使用情况来分析一下问题

对于CPU的使用率，通常用Load Average，也就是负载均值来度量

**负载均值是啥？**

负载是啥，负载就是对CPU使用率的一个计量，均值就是某一段时间内的一个平均值。

**怎么看啊？**

直接输入w命令
<pre class="lang:default decode:true"># w
20:02:51 up 23 days, 8:10, 2 users, load average: 1.20, 1.28, 1.29</pre>
第一位1.20：表示最近1分钟平均负载
第二位1.28：表示最近5分钟平均负载
第三位1.29：表示最近15分钟平均负载

或者uptime命令
<pre class="lang:default decode:true">$ uptime
09:50:21 up 200 days, 15:07, 1 user, load average: 0.27, 0.33, 0.37</pre>
我们一般认为0.00表示无负载，可以理解为CPU空闲，1.00表示CPU满负载，但是注意，1.00是对于单cpu来说的，也就是说，如果是双核，那么这个满负载显示的值应该是2.00，以此类推。

**怎么看我是几核啊**
<pre class="lang:default decode:true ">grep 'model name' /proc/cpuinfo | wc -l</pre>
通过统计cpuinfo的model name信息来算的

**这三个值哪个重要？**

一分钟内突然负载很大没关系，当然如果你要排查也没人拦着，如果15分钟的负载均值超过cpu的数目，就要关注了。

**那什么就是理想负载呢？**

以单个cpu为例，1.00表示cpu满负载运行，没有一点点浪费，实际上，有些管理员认为0.7也许是理想的状态。如果你的经常超过0.7，那么最好查一查。