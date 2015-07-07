title: SQLServer超时时间已到解决和一套控件库
date: 2012-07-13 08:36:56
id: 1253
categories:
  - 文章收藏
tags:
  - C＃
  - 学习
  - 工作
  - 编译
  - 设计
---

 　　在最近的一个程序中，因为频繁的操作数据库，莫名的出现了如下的错误：

　　超时时间已到。超时时间已到，但是尚未从池中获取连接。出现这种情况可能是因为所有池连接均在使用，并且达到了最大池大小。

　　说明: 执行当前 Web 请求期间，出现未处理的异常。请检查堆栈跟踪信息，以了解有关该错误以及代码中导致错误的出处的详细信息。

　　异常详细信息: System.InvalidOperationException: 超时时间已到。超时时间已到，但是尚未从池中获取连接。出现这种情况可能是因为所有池连接均在使用，并且达到了最大池大小。

　　按理说这应该是没有关闭数据库连接或者dataset，可是查了一下。发现都关了啊。遂可以表述为原因不明，但是通过在数据库连接字符串中添加max pool size=512  这个数字可以自己设。就可以了

　　这篇文章短是短了点。。好吧。那顺带分享一套非常精美的C#控件。
[RadControls-For-WinForms 控件2012](http://115.com/file/dptqnr63#RadControls-WinForms-2012-2-608-Dev.msi)
[![](/images/35b3a6023ad34c4bab5564678dffabbcdd53a5da.jpg "wpf")](http://leaverimage.b0.upaiyun.com/24794_o.jpg)
[RadControls-for-WPF控件2012](http://115.com/file/e79q6pju#RadControls-for-WPF-2012-2-0607-Dev.msi)
[![](/images/c3f1eeb8b01e38643d7ce0d2e2716bba20285943.jpg "winform")](http://leaverimage.b0.upaiyun.com/24793_o.jpg)
