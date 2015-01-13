title: Ubuntu 安装phpmyadmin
tags:
  - mysql
id: 356
categories:
  - 学习笔记
date: 2012-04-21 12:06:44
---

   在[lamp环境搭建](http://leaver.me/archives/196.html)这篇文章中，使用apt-get安装了lamp环境，可能你会发现mysql命令行操作不方便。那么需要安装web版的phpmyadmin来辅助了。两步；
1.打开终端 输入
<pre lang="c">
sudo apt-get install phpmyadmin
</pre>
执行过程中我记得会让输入msql的密码。和设置phpmyadmin的密码。phpmyadmin的用户名是root
然后直接访问 http://localhost/phpmyadmin，会发现不能用。因为phpmyadmin被安装在了/usr/share/phpmyadmin/目录。

2.
<pre lang="c">sudo ln -s /usr/share/phpmyadmin/ /var/www/</pre>

建立一个软连接，不喜欢的话，你也把phpmyadmin直接复制到 /var/www/的文件夹下面也可以。

在Ubuntu下面就可以通过http://localhost/phpmyadmin正常使用phpmyadmin了。