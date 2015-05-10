title: wordpress使用NextGEN Gallery 图片展示重复解决
tags:
  - NextGEN Gallery
  - wordpress
id: 127
categories:
  - 学习笔记
date: 2012-04-12 07:35:43
---

博客刚刚建立，找了一个比较漂亮的主题，白色的。也就是[Origin](http://devpress.com/shop/origin/)这个。

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20340_o.png)

然后呢。为了有一个好的图片展示效果，也就是我首页的相册页面。我使用了被大家广泛好评的NextGEN Gallery，在一切完成后，我发现浏览图片的时候会出现重复的效果<!--more-->

[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20341_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20340_o.png)

然后呢。为了有一个好的图片展示效果，也就是我首页的相册页面。我使用了被大家广泛好评的NextGEN Gallery，在一切完成后，我发现浏览图片的时候会出现重复的效果<!--more-->

[![](/images/)](http://leaverimage.b0.upaiyun.com/20341_o.png)
>>>>>>> 换电脑之后重新备份

注意看后面，还有一层效果，也就是说把这层关掉以后，还要再关一次，可以认为图片被展开了两次。

Google了n久未果。。然后

查看了一下主题的说明

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/)](http://leaverimage.b0.upaiyun.com/20342_o.png)
=======
[![](/images/)](http://leaverimage.b0.upaiyun.com/20342_o.png)
>>>>>>> 换电脑之后重新备份

&nbsp;

发现有内置的灯箱效果，这样就和NextGEN Gallery的效果冲突了。就会显示两次，所以需要做的就是在wordpress后台选择<span style="color: #ff0000;">图集-选项-效果-javaScript效果，然后设置为不显示</span>，就屏蔽了NextGEN Gallery的过小。也就只剩下灯箱效果了。