title: 修改Windows系统软件默认安装路径
tags:
  - 学习
  - 总结
  - 操作系统
  - 软件
id: 636
categories:
  - 学习笔记
date: 2012-05-17 06:02:51
---

　　作为一个完全不能容忍windows默认程序都往C:\Program Files\目录里安装的人。每次安装软件的时候，都得手动一个个改到D:\Program Files里。安装软件多了。就hold不住了。

　　其实可以通过注册表使得所有安装程序默认安装到其他盘的。将下列内容保存到一个文本文件里，命名为Mo.reg。然后运行即可。以后安装的程序就会默认安装到D盘的Program Files目录了。你也可以根据需要自行修改

　　<pre lang="c">

　　Windows Registry Editor Version 5.00

　　;修改Windows系统软件默认安装路径

　　[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion]

　　"ProgramFilesDir"="D:\\Program Files"

　　</pre>
效果如下图，安装程序已经默认到D盘了
[![]({{BASE_PATH}}/images/a83d479db2c8d3fac56dc0dacca9fca7329d7c61.jpg "w")](http://leaverimage.b0.upaiyun.com/21644_o.jpg)

update:已知后遗症，我在这样实践了以后，发现vs2010的.net framework 4将会丢失。也就是说就不能新建.net framework 4项目了，具体不明，可能是我当初把VS一部分安装到D盘的缘故，应该可以通过重新安装vs解决，如果你不想折腾。把注册表改回去就可以了。