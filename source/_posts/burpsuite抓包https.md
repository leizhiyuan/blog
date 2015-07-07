title: burpsuite抓包https
tags:
  - 互联网
  - 安全
id: 3674
categories:
  - 学习笔记
date: 2014-06-29 06:43:48
---

最近工作需要，需要burpsuite抓下https的包

burpsuite抓包https的时候，如果是https协议，火狐会不信任证书

&nbsp;

[![untrust](/images/14c5d2aedaa02e325fe21c03758796234ed83576.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/untrust.jpg)

这个时候点击我已充分了解可能的风险，然后添加例外，弹出图中的对话框

[![step](/images/4adf7f8902f07ea98a728a2d71270906208bfea6.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/step.jpg)

点击查看，进入证书管理企业免，选择图中的PortSwigger CA根证书，选择导出，保存一下这个证书

[![export](/images/6e81972ac7283935e22a260f92ab4a582780eb73.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/export.jpg)

然后打开火狐的选项，进入高级-&gt;证书-&gt;查看证书-&gt;证书机构-&gt;导入，选择刚才的证书

[![import](/images/3e43de2ca0a2c88519035a3d39a60fb8757c4da9.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/import.jpg)

，最后，记住选中信任此CA证书标识的网站，ok

[![finish](/images/070d8695abea24e9e8bf11e162f12afccaa5385f.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/finish.jpg)

现在再抓包，可以看到https也抓到了。

[![capture](/images/cf1f9324b8934ba12cab8ea0a19a530897e206e3.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/capture.jpg)

&nbsp;