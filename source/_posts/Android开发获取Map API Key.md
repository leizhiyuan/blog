title: Android开发获取Map API Key
tags:
  - android
  - 原创
  - 大学
  - 软件
id: 2131
categories:
  - 学习笔记
date: 2012-10-21 11:01:14
---

地图应用使用com.google.android.maps这个包。通过MapView控件使用。但是之前需要申请一个用于开发的API Key，这个key会和当前的计算机用户绑定。然后通过这个key去官方申请就可以拿到一个开发用的api key了
&lt;1&gt;首先找到用户的debug.keystore文件，可以再”运行“里面搜debug.keystore；如：c:\users\Administrator\.android\debug.keystore

&lt;2&gt;接下来获取MD5指纹，网上很多说的有误。貌似新版默认是出现sha1加密的。通过添加-v 参数会显示所有。

首先运行cmd,在dos界面里，输入
<pre class="lang:apache decode:true crayon-selected">keytool -list -v -keystore c:\users\Bystander\.android\debug.keystore</pre>
命令，然后会让你输入keystore密码，

输入：android，之后，会出现指纹认证MD5，如下：

[![]({{BASE_PATH}}/images/81f1a9c78815b22e8b390275737d4d1c74212447.jpg "md5")](http://leaverimage.b0.upaiyun.com/28249_o.jpg)
&lt;3&gt;去官方生成真正的api key
访问 [Sign Up for the Android Maps API](https://developers.google.com/android/maps-api-signup?hl=zh-CN) 输入那串值，同意条款，确定后要求用Google帐号登录。然后会拿到一个key。ok