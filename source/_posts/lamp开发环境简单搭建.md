title: lamp开发环境简单搭建
tags:
  - linux
  - php
id: 196
categories:
  - 学习笔记
date: 2012-04-15 17:54:27
---

　　因为一些事情，要去学习php开发。所以呢。今天就先搭建一下php开发环境，其实windows下搭建相对比较简单，也有一键安装包。比如[AppServ](http://www.appservnetwork.com/)，但是因为考虑到以后的一些事情，于是还是采用LAMP开发环境。

　　我是用Ubuntu来做，本地刚好有Ubuntu的镜像。。虚拟里面来测试。首先就是在虚拟机里安装Ubuntu。这个不多说。大家都会。安装好以后。登陆进来。命令行或是图形界面都可以。

　　新版本的Ubuntu貌似是没了新立得管理器。所以使用命令来安装更简单。打开终端。切换到root。如果不能切换到root。[参考此文](http://leaver.me/archives/205.html)。切换到root后。输入

<pre lang="php">apt-get install apache2 mysql-server mysql-client php5 php5-gd php5-mysql</pre>

　　回车后就开始自动下载了。大概几分钟后就会出现MySql的安装设置界面

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20538_o.png)
　　输入你想设置的mysql的登录密码。然后需要再输入一遍

[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20539_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20538_o.png)
　　输入你想设置的mysql的登录密码。然后需要再输入一遍

[![](/images/)](http://leaverimage.b0.upaiyun.com/20539_o.png)
>>>>>>> 换电脑之后重新备份
ok。等会就安装完成了。。就这么简单。。
然后进行一些后续的设置

　　默认网站的目录在/usr/www.这个目录的权限如下图。
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20541_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20541_o.png)
>>>>>>> 换电脑之后重新备份

　　为了以后方便。设置为777会更好一些。。执行如下命令：
<pre lang="php">sudo chmod 777 /var/www </pre>

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20542_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20542_o.png)
>>>>>>> 换电脑之后重新备份
　　然后是启用 apache的 mod_rewrite 模块 
输入如下命令：
<pre lang="php">sudo a2enmod rewrite</pre>
　　然后继续输入如下命令来重启 Apache服务器： 
<pre lang="php">sudo /etc/init.d/apache2 restart</pre>
　　Apache重启后我们可以测试一下，在 /var/www目录下新建文件 test.php，写入代码： <pre lang="php"> <?php phpinfo(); ?> </pre>保存，在地址栏输入 http://127.0.0.1/test.php 或 http://localhost/test.php ，如果正确出现了如下 php 配置信息则表明 LAMP Apache已经正常工作了
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20543_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20543_o.png)
>>>>>>> 换电脑之后重新备份

　　还可以测试一下mysql是否正常。这个直接在终端下输入 
<pre lang="php">mysql -u root -p</pre>
　　然后根据提示输入密码就出现如下的图
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20544_o.png)
　　表示已经登录到mysql了。说明mysql可以了。可以继续输入 <pre lang="php">show databases;</pre>来显示所有的数据库。
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20547_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20544_o.png)
　　表示已经登录到mysql了。说明mysql可以了。可以继续输入 <pre lang="php">show databases;</pre>来显示所有的数据库。
[![](/images/)](http://leaverimage.b0.upaiyun.com/20547_o.png)
>>>>>>> 换电脑之后重新备份

参考文章：[http://blog.csdn.net/xiaojianpitt/article/details/6326834](http://blog.csdn.net/xiaojianpitt/article/details/6326834)