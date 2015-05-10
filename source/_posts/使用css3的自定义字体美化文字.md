title: 使用CSS3的自定义字体美化文字
tags:
  - 前端
  - 大学
  - 学习
  - 编程
  - 设计
id: 3144
categories:
  - 学习笔记
date: 2013-07-17 14:45:45
---

之前看到一些设计师的主题的字体很美，下载下来发现使用了css3的自定义字体，可以用来显示服务器上的字体，非常方便，学习了一下

1.首先得到字体

这个方法很多，本机的字体，一些国外的免费网站，比如这个：[http://www.dafont.com](http://www.dafont.com/)，下载后的字体一般为ttf格式，ttf字体被很多浏览器支持，但是，IE不支持，为了兼容性，需要为IE单独设置字体文件，格式必须为eot，所以我们需要转换字体，使用在线工具，比如[http://www.kirsle.net/wizards/ttf2eot.cgi](http://www.kirsle.net/wizards/ttf2eot.cgi)，当然类似的网站有很多，根据个人爱好，随意。

&nbsp;

2.添加内容

这里，我写一个简单的html文件，内容为
<pre class="lang:default decode:true">&lt;body&gt;
&lt;p class="test"&gt;bystander&lt;/p&gt;
&lt;/body&gt;</pre>
在没有设置customFont这个类的css之前，字体就是默认的字体了。

&nbsp;

3.设置css样式
<pre class="lang:default decode:true">@font-face
{
font-family:myFont;/*主流浏览器可用*/
src:url("PONCTUATION.ttf");
}
@font-face
{
font-family:myFont;/*兼容IE*/
src:url("PONCTUATION.eot");
}
.test
{
font-family:myFont;
font-size:40px;
}</pre>
显示效果就是这样的了...只是用来演示的一个字体。

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/0aa0db5ed4bc986d61cfbb9e1e421a2226c94f9b.png)](http://leaverimage.b0.upaiyun.com/38421_o.png)
=======
[![](/images/0aa0db5ed4bc986d61cfbb9e1e421a2226c94f9b.png)](http://leaverimage.b0.upaiyun.com/38421_o.png)
>>>>>>> 换电脑之后重新备份

&nbsp;

因为浏览器是要自动下载这个字体文件的，所以对于英文字体没啥问题，英文字体一般这个字体文件在100k左右，和一张图片比起来，基本算不是问题，但是对于中文字体，包一般在10M-20M左右，这样是不现实的，我的想法是，可以自己制作字体包，这样只需要满足常用的一些汉字就行了，大大减少包的大小，然后去找了一下，发现了[http://www.high-logic.com/font-editor/fontcreator.html](http://www.high-logic.com/font-editor/fontcreator.html)这个软件，是可以直接编辑字体包的，也可以创建字体包，有空了用来试试.