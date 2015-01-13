title: "[藏]隐藏在PNG图像元数据中的新iFrame注入攻击"
tags:
  - 上海
  - 互联网
  - 安全
id: 3475
categories:
  - 文章收藏
date: 2014-02-09 09:23:12
---

这个攻击方式很旧了，但手法相当不错，来自[乐嵌网](http://www.lembed.com/new-iframe-injections-leverage-png-image-metadata/)

我们一直在努力保持领先的最新趋势，今天我们发现了一个非常有趣的事，这或者是我们之前没有发现，或者是刚发生的。 我们只能说这是新发现的。

&nbsp;

我们都了解的iFrame注入攻击，对吧？

&nbsp;

**了解一个iFrame注入**

今天的iFrame 是非常标准的HTML标记，它是在自己网页中嵌入其他网站内容的简单方式。 被几乎所有的浏览器支持和百万计的网站使用，使用AdSense吗？ 那么你的网站中就有一个iFrame。

我知道这东西很好，然而总是福祸相惜。

今天的攻击，特别是当我们谈论路过式下载，首选是利用IFRAME标记。 它简单方便，只需简单的属性修改，攻击者可以稳妥的从另一个站点嵌入代码，并通过客户的浏览器不知不觉的加载。

&nbsp;

像这样：

[![iframe-sample]({{BASE_PATH}}/images/3da10af5e9e3a897cf39927ee28a0106f297880a.png)](http://leaverimage.b0.upaiyun.com/2014/02/iframe-sample.png)

&nbsp;

攻击者通常从其他网站嵌入的恶意文件，通常是一个PHP文件或类似的形式。 当然，这并不是唯一的方法，但是是最普遍的。 从检测和修复的角度来看，这往往很容易修复。

&nbsp;

**新的iFrame注入法**

然而今天，我们发现一个有趣的iFrame注入类型。

它的特殊之处不是在iframe标签中嵌入的内容，而是它分发恶意软件的方式。 你会看到，攻击者将威胁隐藏在PNG文件中。

我几乎可以听到很多你的窃笑，PFF .. 这不是新的…. 但问题是在细节上我的朋友。

&nbsp;

如下，iFrame加载了一个有效的文件，没有什么恶意，一个叫jquery.js的JavaScript文件。这一切看上去都很好。你要细细的找：

[![blog_injection1-494x650]({{BASE_PATH}}/images/7f64ba23d5fd4d5763ff921134e11beede62faeb.png)](http://leaverimage.b0.upaiyun.com/2014/02/blog_injection1-494x650.png)

起初，很多人会说我们傻。 这些代码很好，没看见什么问题，对不对？ 然后，我们注意到了这个小函数，loadFile（）。 函数本身并不奇怪，但事实上，它是装载一个PNG-**var strFile = ‘./dron.png**。 你会被它惊讶良久，它才是一个真正的黑手。

&nbsp;

自然，下一步就是好奇的打开dron.png文件。 会有什么可怕的事情发生吗？

&nbsp;

噢，什么都没有，太无聊了，这纯粹是浪费时间。

[![Sucuri-iframe-png]({{BASE_PATH}}/images/0d8a2bcd6d6e5a6ac87f8858fe8f0a81878d6bbc.png)](http://leaverimage.b0.upaiyun.com/2014/02/Sucuri-iframe-png.png)

但是，请等一下，我发现这里有个有趣的小循环。

[![Sucuri-PNG-Decoding-Loop]({{BASE_PATH}}/images/8c17a78234586bebb89f023d8023b6b69d9f574b.png)](http://leaverimage.b0.upaiyun.com/2014/02/Sucuri-PNG-Decoding-Loop.png)

嗯，这是确实挺奇怪的，这是一个解码循环。 为什么要一个循环的解码PNG文件呢？

&nbsp;

作为一个优秀的研究人员，采取最简单的方式，我可以将它加载到简单的测试页上 并提取图像内容。搞定！

&nbsp;

测试页上加载它之后这，我们获得了strData变量：

[![sucuri-png-iframe-payload]({{BASE_PATH}}/images/4a1a1266a40ff50c6d0328eea9c9e849e043ae05.png)](http://leaverimage.b0.upaiyun.com/2014/02/sucuri-png-iframe-payload.png)

&nbsp;

看到它在干嘛了吗？

它做了个iFrame注入，并把它嵌入到PNG的元数据中，像一个新的分配机制。

&nbsp;

有两点需要特别注意了，它使用**createElement**做了一个**iFrame**标签**，**然后设置**elm.style.position.left 和 elm.style.position.top **属性的值为-1000px，把iframe放到可视区域之外。这些值都是负值，在浏览器中根本看不到。但是你知道谁能看到吗？只有浏览器和Google能看到。这就是下载驱动和搜索引擎感染（SEP)攻击的小伎俩。

&nbsp;

最后我们在**elm.src**元素中发现了真正的威胁**。**

&nbsp;

**做的这么独特到底有什么用呢？**

它尽力隐藏真正的威胁的水平是独特的。现在的杀毒软件不会解码图像元数据，直到JavaScript文件加载完毕就停止扫描。不会追踪cookie文件，对于攻击者来说，这太好了，使它们很难被发现。

&nbsp;

记住一点，尽管我们这里谈论的是PNG文件，这些方法和概念也可以应用到其他类型图像中。重要的一点是：留意你的web 服务状态，了解哪些修改和未修改的文件并确保漏洞未被利用。

&nbsp;

通常情况下，一些新的侦查和排障需要一定的时间。 现在我们的 网站恶意软件扫描器能发现它吗 ？ 绝对可以！

&nbsp;

不要笑了，朋友们！

原文：[new-iframe-injections-leverage-png-image-metadata](http://blog.sucuri.net/2014/02/new-iframe-injections-leverage-png-image-metadata.html) by Peter Gramantik