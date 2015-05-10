title: ubuntu终端su认证失败解决
tags:
  - linux
id: 205
categories:
  - 学习笔记
date: 2012-04-15 18:09:10
---

　　这个以前碰到过。不过今天又遇到了。记录一下。Ubuntu 安装后，root用户默认被锁定，不允许登录，也不允许“su”到 root。对于开发人员来说貌似有些麻烦了。。

　　解决方法：打开终端。输入<pre lang="php">sudo passwd </pre>回车，然后输入安装ubuntu时设置的密码。回车后要求输入新密码。新密码可以和安装时的密码相同。所以继续输吧。然后确认一次。就可以了

<<<<<<< HEAD
　　[![]({{BASE_PATH}}/images/5db34965eb544ff3fdbab59aafcc13ec0cee0e19.png)](http://leaverimage.b0.upaiyun.com/20548_o.png)
=======
　　[![](/images/5db34965eb544ff3fdbab59aafcc13ec0cee0e19.png)](http://leaverimage.b0.upaiyun.com/20548_o.png)
>>>>>>> 换电脑之后重新备份

  然后正常su root 就可以切换到root了