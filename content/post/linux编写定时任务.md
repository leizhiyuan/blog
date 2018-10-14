---
title: linux编写定时任务
tags:
  - linux
  - shell
  - 工作
id: 3575
categories:
  - 学习笔记
date: 2014-03-29 10:31:31
---

linux中定时任务用来执行一些周期性的自动化的任务，比如有些人可能用来定期备份，也可能是定期检查一下特殊文件的签名，如果不一致，就报警，检测入侵。

cron是linux下的定时执行工具

这个工具的几个命令是这样的
<pre class="lang:default decode:true">/sbin/service crond start //启动服务

/sbin/service crond stop //关闭服务

/sbin/service crond restart //重启服务

/sbin/service crond reload //重新载入配置</pre>
注意，这几个服务都是要以root权限才能运行的，很多时候，只要我们可能只是一个低权限的用户，那么我们要执行一些定时任务的时候，可以这样做

直接通过这个命令来编辑，无需root用户
<pre class="lang:default decode:true">crontab</pre>
首先添加定时任务
<pre class="lang:default decode:true">crontab -e</pre>
打开之后按如下的格式编写
<pre class="lang:default decode:true">*/1 * * * * ls &gt;&gt; /tmp/ls.txt</pre>
从左到右一次表示

分钟 一小时的第几分 0-59
小时 一天的第几小时 0-23
日期 一个月的的第几天 1-31
月份 一年的第几个月 1-12
周几 一周的第几天 0-6

/1表示每一天 /2表示每两天，直接*的话就表示每天/每小时这样

写完之后，wq保存退出

然后
<pre class="lang:default decode:true">crontab -l //列出当前的所有调度任务</pre>
可以看到自己的定时任务了，然后就不要做什么操作了，操作系统定时会读取配置的，编辑完成之后，我们的定时任务过一会就会生效了。

有时候，可能还要把结果信息和一些错误信息也写入
<pre class="lang:default decode:true">30 5 * * * ls &gt;&gt;/result/test 2&gt;&amp;1

注：2&gt;&amp;1 表示执行结果及错误信息。</pre>
这里就是说明天的5点50执行一次ls命令，并把结果追加到文件

如果我想每天5点30和17点30都执行一次呢，使用逗号隔开就行了
<pre class="lang:default decode:true">30 5,17 * * * ls &gt;&gt;/result/test 2&gt;&amp;1</pre>
如果是某个时间段呢
<pre class="lang:default decode:true">30 5-17 * * * ls &gt;&gt;/result/test 2&gt;&amp;1</pre>
这样5-17点钟的每个30分到会执行

如果是一些特殊的时间点，那么有更简单的方法，比如每月0点或者每天0点执行一次
<pre class="lang:default decode:true">@monthly ls &gt;&gt;/result/test 2&gt;&amp;1</pre>
<pre class="lang:default decode:true">@daily ls &gt;&gt;/result/test 2&gt;&amp;1</pre>
使用如上的关键字