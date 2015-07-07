title: WCF双工通信示例
tags:
  - C＃
  - WCF
  - 大学
  - 开发
  - 总结
  - 软件
id: 1438
categories:
  - 学习笔记
date: 2012-09-07 22:22:30
---

　　这两天在看WCF的书籍。就参考书上的代码写了这个例子。不得不说。书上有些错误的地方。。运行明显报错。改了一下。顺利通过。

　　先运行Hosting。然后运行Client。可以看到效果。不过不知道为什么会有如下的一个提示：

　　目标程序集不包含服务类型。可能需要调整此程序集的代码访问安全策略。

　　点击确定后并不影响程序运行。。但是也是个问题。。找了一下解决方法。都没有解决。。可能是我新建契约服务的时候，删掉了默认的IService配置。

//update:此问题解决了。是因为默认的app.config太多。对于典型的四层结构。需要删除契约和服务中的app.config。。

　　暂时先放着吧。

　　运行截图：

　　[![](/images/35cc6b919f9d513bf612503ffcac5b3448f20f37.jpg "wcfExample")](http://leaverimage.b0.upaiyun.com/26775_o.jpg)

　　示例代码下载：[Lazy.Duplex](http://dl.vmall.com/c0c44hq61p)