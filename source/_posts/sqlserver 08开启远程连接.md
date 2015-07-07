title: SqlServer 2008开启远程连接
tags:
  - windows
  - 大学
  - 学习
  - 开发
  - 总结
id: 1386
categories:
  - 学习笔记
date: 2012-08-20 02:48:22
---

　　对于需要外部访问数据库的操作，需要开启sql server的远程连接。没经验的我等Google之。。大部分操作按照[SQL Server 2008 R2如何开启数据库的远程连接](http://jingyan.baidu.com/article/6c67b1d6ca06f02787bb1ed1.html/)来操作

　　但是。有一些很小的细节需要注意。我的数据库是Sql Server 2008 Express版。这个是VS自带的。为了管理方便，可以安装[SQL Server® 2008 Management Studio Express](http://www.microsoft.com/zh-cn/download/details.aspx?id=7593) 安装过程不多说。安装完成后，直接打开

　　[![](/images/e03ce8654db7fcc6eb18d317acd95e7e78f974e8.jpg "login")](http://leaverimage.b0.upaiyun.com/26088_o.jpg)

　　服务器名称默认是空的。。“.”好像是不行的，这时候点击右边箭头。更多，本地和远程服务器。在远程服务器里可以找到。点击就可以了。

　　我按照文章改完。sa还是登不上。。然后又试了一些

　　[![](/images/245e1caa369d6d4473c100bb48e6bcb069793727.jpg "role")](http://leaverimage.b0.upaiyun.com/26089_o.jpg)

　　如上图右键sa，属性，常规里设置sa密码。不要太简单。状态里的登录选项设为启用。ok。。我碰上的问题就这几个。