---
title: "SecureRandom第一次生成随机数非常慢"
date: 2015-06-30 19:18:36
tags:
  - 开发
  - 工作
categories: 
  - 学习笔记
---

最近发现某个系统在第一次做操作的时候非常缓慢,逐步定位打印更加详细的日志后,发现问题是使用了SecureRandom 这个类来获取随机种子,这个类第一次初始化的时候setSeed的值,非常缓慢,偶尔出现, 排查的过程就是二分,不断定位具体的代码,最终定位

也就是说根本原因是SecureRandom 这个jre的工具类的问题.具体的bug搜索之后,见

[http://bugs.java.com/view_bug.do?bug_id=6521844](http://bugs.java.com/view_bug.do?bug_id=6521844)

详细的解释过程:
参考: [http://www.websina.com/bugzero/faq/securerandom-slowness.html](http://www.websina.com/bugzero/faq/securerandom-slowness.html)

>Q: Why the SecureRandom generateSeed is so slow or even hang on Linux OS?

>A: When you login, it hangs or takes more than a minute to get the response.
       If your server is on a Linux OS, the culprit here is SecureRandom generateSeed()
       which uses /dev/random to generate the seed. However, /dev/random is a blocking
       number generator and if it doesn't have enough random data to provide, it will
       simply wait until it does, which forces the JVM to wait. Keyboard and mouse input
       as well as disk activity can generate the randomness or entropy needed. But on a
       server that lacks such activies, the problem may arise.


大意是说当使用SecureRandom 的时候默认使用的/dev/random 文件来生成种子,如果没有足够的种子数据,(这些数据是由键盘,鼠标,磁盘活动等产生的,如果没有这些活动,就没有足够的数据)会一直等待,导致jvm卡住,耗费比较长的时候.由于出现问题的这个环境的确是最近没什么操作,而且复现的操作也是隔个一个晚上,重新做业务才会出现.

文中给出了一种解决方案.

1.  启动参数添加 -Djava.security.egd=file:/dev/./urandom,验证是可以的.
2.  修改随机数获取方式

这里urandom是啥呢,引用自[维基](https://zh.wikipedia.org/wiki//dev/random)
>/dev/random的一个副本是/dev/urandom（“unlocked”，非阻塞的随机数发生器[4]），它会重复使用熵池中的数据以产生伪随机数据。这表示对/dev/urandom的读取操作不会产生阻塞，但其输出的熵可能小于/dev/random的。它可以作为生成较低强度密码的伪随机数生成器，不建议用于生成高强度长期密码。

类似案例: SecureRandom 导致tomcat启动过慢.
[https://wiki.apache.org/tomcat/HowTo/FasterStartUp#Entropy_Source](https://wiki.apache.org/tomcat/HowTo/FasterStartUp#Entropy_Source)
从文中看到说java8已经提升了这个性能.但是一般生产环境的java版本是不会改的.
