---
title: oom介绍
date: 2017-02-03 15:04:04
tags:
  - java
  - 工作
categories:
  - 学习笔记
---


oom 之前知道, 但是并不是很了解,最近遇到了由 oom 引发的问题,所以学习记录一下.

OOM-killer：Out-of-Memory (OOM) Killer是一种保护机制，用于当内存严重不足时，为了系统的继续运转，内核迫不得已挑选一个进程，将其杀死，以释放内存，缓解内存不足的问题。
可以看出这种方式对进程的保护是有限的，不能完全的保护进程的运行。


## 如何知道是否发生了 oom
两种方法,第一种,查看 /var/log/messages,会有类似
````
Out of memory: Kill process 9682 (mysqld) score 9 or sacrifice child
Killed process 9682, UID 27, (mysqld) total-vm:47388kB, anon-rss:3744kB, file-rss:80kB
httpd invoked oom-killer: gfp_mask=0x201da, order=0, oom_adj=0, oom_score_adj=0
httpd cpuset=/ mems_allowed=0
Pid: 8911, comm: httpd Not tainted 2.6.32-279.1.1.el6.i686 #1
````
这样的标识,说明发生了 oom,关键就是 kill process, 所以可以这样

`sudo cat /var/log/messages | grep -i"killed process"`

另一种是通过dmesg来查看
`dmesg | egrep -i 'killed process'`
这个命令查看的 oom 的时间里是时间戳的形式,如果你的 dmesg 没有-T这个时间的选项,那么就需要通过

````
date -d "1970-01-01 UTC `echo "$(date +%s)-$(cat /proc/uptime|cut -f 1 -d' ')+12288812.926194"|bc ` seconds"
````

来转换成可读的时间了.


## oom 的原理

其中涉及到有三个相关文件：

* /proc/$PID/oom_adj
* /proc/$PID/oom_score
* /proc/$PID/oom_score_adj
其中 oom_score 表示最终的分数，该分数越大，越可能被 Killer 杀掉。

而 oom_adj 是调整分数的，可以设置为负值，会对 oom_score减分。

从Linux 2.6.36开始都安装了/proc/$PID/oom_score_adj，此后将替换掉/proc/$PID/oom_adj。即使当前是对/proc/$PID/oom_adj进行的设置，在内核内部进行变换后的值也是针对/proc/$PID/oom_score_adj设置的。可以参见[feature-removal-schedule](https://github.com/tinganho/linux-kernel/blob/master/Documentation/feature-removal-schedule.txt)这里 171行. 

通过 cat /proc/$PID/oom_score 可以查看进程的得分


打分算法在这里
https://github.com/torvalds/linux/blob/master/mm/oom_kill.c

从上面的 oom_kill.c 代码里可以看到 oom_badness() 给每个进程打分，根据 points 的高低来决定杀哪个进程，这个 points 可以根据 adj 调节，root 权限的进程通常被认为很重要，不应该被轻易杀掉，所以打分的时候可以得到 3% 的优惠（adj -= 30; 分数越低越不容易被杀掉）。我们可以在用户空间通过操作每个进程的 oom_adj 内核参数来决定哪些进程不这么容易被 OOM killer 选中杀掉。比如，如果不想 MySQL 进程被轻易杀掉的话可以找到 MySQL 运行的进程号后，调整 oom_score_adj 为 -15（注意 points 越小越不容易被杀）：范围是从-1000 到 1000,参考[这里](https://github.com/torvalds/linux/commit/a63d83f427fbce97a6cea0db2e64b0eb8435cd10#include/linux/oom.h)

## 说明

也就是说rot 进程也并没有太大的优惠.主要还是取决于占用内存的多少.这就说明,如果 A,B 两个进程. B 属于正常进程,占用系统最多的进程.但是 A 是其他进程,如果 A 突然占用内存升高,导致需要触发 oom, 那么由于打分算法的存在,也会导致 B 被杀.

## 如何查看当前进程分数

两种.一种是
`ps -eo pid,comm,pmem --sort -rss | awk '{"cat /proc/"$1"/oom_score" | getline oom; print $0"\t"oom}'`

另一种是

````
#!/bin/bash
for proc in $(find /proc -maxdepth 1 -regex '/proc/[0-9]+'); do
    printf "%2d %5d %s\n" \
        "$(cat $proc/oom_score)" \
        "$(basename $proc)" \
        "$(cat $proc/cmdline | tr '\0' ' ' | head -c 50)"
done 2>/dev/null | sort -nr | head -n 10
````

## 能否关闭

OOM Killer的关闭与激活方式：
# echo "0" > /proc/sys/vm/oom-kill
# echo "1″ > /proc/sys/vm/oom-kill


## 参考

[Linux 找出最有可能被 OOM Killer 杀掉的进程](https://github.com/Yhzhtk/note/issues/31)
[理解和配置 Linux 下的 OOM Killer](http://www.vpsee.com/2013/10/how-to-configure-the-linux-oom-killer/)