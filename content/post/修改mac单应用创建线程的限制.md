---
title: 修改mac单应用创建线程的限制
date: 2016-08-14 17:33:52
tags:
  - 工具
  - 生产力
  - 记录
categories:
  - 学习笔记
---

最近遇到一个问题,公司的 java 服务端应用,启动后,通过 `jstack pid |grep nid -c`,可以看到大概创建了2044个线程,然后此时应用就会报错,提示无法创建更多线程, jvm 开始抛错.
查看 mac 的内存,发现还是够的.因为一般认为可创建的线程数=(总内存-其他占用的内存)/线程大小,所以内存够的情况下,应该是能创建的.


google 一圈,发现mac 对单线程创建的线程是有限制的.理由应该是为了保持系统稳定性.主要有两个参数

`sysctl kern.num_threads` 这个可以看一下,说明了系统能够创建的总共的线程,单个应用能够创建的线程是`sysctl kern.num_taskthreads`,第二个参数就是导致我们创建不出来更多线程的原因,
因为2044+一些 gc 的线程,基本上刚刚达到这个极限.

那么要么改程序,要么改参数.改程序这是不可能的..因为只有 mac 会有这个问题..该参数尝试通过`sudo sysctl -w kern.num_taskthreads=4096`,修改,会发现提示是只读属性.google 了一圈,无解.

最终意外解决..


参考这里[开启性能模式](https://support.apple.com/en-us/HT202528) 


1. `nvram boot-args`
2. `sudo nvram boot-args="serverperfmode=1 $(nvram boot-args 2>/dev/null | cut -f 2-)"`
3. 重启

如果想要恢复的话: `sudo nvram boot-args="$(nvram boot-args 2>/dev/null | sed -e $'s/boot-args\t//;s/serverperfmode=1//')"`

当时各种搜索,加打apple 支持电话.无解.搜索意外看到这个说明,说开启之后,可以支持更多服务应用之类的.猜测应该会改这个值..果然..改完之后,直接重启,这个限制会变成5000..完美解决..理论上,应该通过继续修改这个参数
是可以自定义这个值的.不过还没尝试. 