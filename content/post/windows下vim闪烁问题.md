---
title: windows下vim闪烁问题
tags:
  - windows
  - 学习
  - 操作系统
id: 514
categories:
  - 学习笔记
date: 2012-05-07 19:21:59
---

　　今天打开我的vim才发现，界面隔几秒会闪烁。虽说貌似能够起到防止眼睛疲劳的效果，但我实在是hold不住啊。不行，搜索。。
首先有这个问题的人不多。首先找到了[这篇文章](http://hi.baidu.com/chrisyue/blog/item/ebc7b8de373c1048cdbf1af9.html)，但是作者不知道怎么想的。只说了原因，没有给出解决方法。继续搜索关键字cursorcolumn，结果找到了[这篇文章](http://yyq123.blogspot.com/2012/01/vim-cursor.html)，按着说明来了一下
<pre lang="php">set cursorline cursorcolumn</pre>
没效果。依然闪烁。
　　好吧。如果是插件的问题。于是我删掉了所有的插件包括写入的配置。依然不行。于是还是采用排除法，一行行删掉配置文件。最后定位到
<pre lang="php">set guifont=Arial_monospaced_for_SAP:h9:cANSI </pre>
　　这是设置字体的，不太明白为什么会出现这样的情况。怀疑是字体的原因，于是换个字体，依然闪烁。。好吧。就这样吧。删掉算了。