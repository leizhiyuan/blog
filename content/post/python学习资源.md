---
title: Python学习资源
tags:
  - python
id: 3715
categories:
  - 学习笔记
date: 2014-07-26 16:19:18
---

最近突然觉得需要学习一个轻便的语言，每次写个小工具啥的还要打开eclipse，很是不方便。于是学习了一下python，看看官方文档基本就可以上手了，剩下的就是多用了，个人用python写了个调用websevice的东西，suds这个库3.x已经不维护了，坑爹，需要使用[http://pypi.python.org/pypi/suds-jurko](http://pypi.python.org/pypi/suds-jurko) 这个库代替，主要是为了批量测试mock的连通性的，

另外是python3.x和python2.x差异是在很大，我选了3..., 刚学习的人还是建议从2.x开始吧，否则很多源代码你下载回来基本运行不了。

本文是我在学习过程中看到的不错的文档，希望有所帮助。

先看官方文档，英文版觉得有难度看下中文版：[Python3.4 入门指南](http://www.pythondoc.com/pythontutorial3/)

然后写写代码，小例子看看[Python快速教程](http://www.cnblogs.com/vamei/archive/2012/09/13/2682778.html)

遇到问题，可以去看看：[stackoverflow python 百问](https://github.com/wklken/stackoverflow-py-top-qa)

刚开始，你可能不知道module，package，这些都傻，先看看这篇文章[python模块](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868200171577d6385bb5b4f4875bee9cbf0f0fa29c5000)

如果你比较纳闷那个命名中的双下划线，但下划线都啥意思，看看[单下划线和双下划线区别](http://igorsobreira.com/2010/09/16/difference-between-one-underline-and-two-underlines-in-python.html)

之后，你可能想看看一个系统的组织，那么看看[python最佳实践指南全版](http://docs.python-guide.org/en/latest/)

如果你觉得上面的太多，看看[中文的摘要版](http://wklken.me/posts/2013/11/25/summary-of-the-hitchhikers-guide-2-python.html)

后来我看到一个yield，不太明白，于是我看到了[Python yield 使用浅析](http://www.ibm.com/developerworks/cn/opensource/os-cn-python-yield/)，只能说写的真是好，就是迭代器生成的

想看看正则？看看[python正则表达式指南，](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)非常全面

我只是想发个http请求，如果你用了python3.x，报错，Import error: No module name urllib，那么看看[urllib](http://stackoverflow.com/questions/2792650/python3-error-import-error-no-module-name-urllib)这个坑，你可能要看看，你没有看错，他们合并了。。

[ Dive into Python ](http://woodpecker.org.cn/diveintopython/toc/index.html)中文也看看，不要在意细节。

最后好像看看[**Expert Python** Programming](http://book.douban.com/subject/3285148/)，因为我没看。