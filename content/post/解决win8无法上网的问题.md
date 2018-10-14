---
title: 解决win8无法上网的问题
tags:
  - windows
  - 互联网
  - 总结
id: 2304
categories:
  - 文章收藏
date: 2012-11-21 16:29:20
---

昨天晚上@虎振兴同学装了win8.。结果悲剧了。症状为连接宽带可以连接上。上qq也正常。但是。只要打开网页。就会自动断网。再连接就会提示651错误了。网上大多说是驱动不兼容。但是解决的方法大部分是不对的。下面结合网上的给大家说一说。。

网卡驱动目测是都是美满公司，也就是Marvell 的Yukon系列网卡驱动的问题。首先下载一个旧版本的驱动（[32位下载](http://pan.baidu.com/share/link?shareid=157758&uk=1493685990)/[64位下载](http://pan.baidu.com/share/link?shareid=157761&uk=1493685990)）
然后按下图操作，第一步是打开计算机-管理。。各种姿势只要打开了计算机管理就可以了。
[![](/images/64492d91b55bcb36bfca307f79e7085701e4d385.jpg "1")](http://leaverimage.b0.upaiyun.com/29531_o.jpg)
在这里稍微记一下这个名字。Marvell Yukon  88exxxxx  PCI-E Fast Ethernet..
[![](/images/f62bc3bc8f6c801bb02b7ac3b5828cd8b8949409.jpg "2")](http://leaverimage.b0.upaiyun.com/29542_o.jpg)
找到网络适配器，右键更新驱动程序。
[![](/images/de07155b61319d7d4bcd6850502b67cf08e010e7.jpg "3")](http://leaverimage.b0.upaiyun.com/29532_o.jpg)
[![](/images/31566293ba5a32fff3f1654f50247e65824c1def.jpg "4")](http://leaverimage.b0.upaiyun.com/29534_o.jpg)
[![](/images/b77e3594df179d35a82777375611b20549290884.jpg "5")](http://leaverimage.b0.upaiyun.com/29535_o.jpg)
注意记下兼容的网卡。名字和第二步的差不多的那个，点击从磁盘安装，选择下载后驱动的解压的安装文件，如图
[![](/images/925d77e1da47d263c1e41785765b5af2b6152818.jpg "6")](http://leaverimage.b0.upaiyun.com/29536_o.jpg)
到这一步以后，点击打开，可能会出现一个驱动列表。这是时候选择一个和兼容列表名字一样的。88e这部分不一样。如果找不到，也可以找类似的，比如途中给出的后两位是39.我装的是40也没问题。这个是驱动的历史版本。然后就可以了
[![](/images/02f918179789c33bccb038983bae0125c0d0d988.jpg "9")](http://leaverimage.b0.upaiyun.com/29538_o.jpg)

最后。提醒各位童鞋。win8整体还是很不错的。不过呢。对于我来说。metro界面和正常的界面的傻傻分不清楚的模式。令我很是蛋疼。。所以暂时没有考虑换到win8.。。