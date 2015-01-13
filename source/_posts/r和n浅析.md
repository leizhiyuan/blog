title: "\r和\n浅析"
tags:
  - C++
  - linux
  - windows
  - 大学
  - 学习
id: 2843
categories:
  - 学习笔记
date: 2013-04-05 13:29:04
---

这两个转义字符最初学习C++的时候看到了,当时没多想，后来某一天突然想起来，回车不就是换行吗？这不是多此一举吗？今天又看到，索性查了下相关资料，整理一下，留作记录.

关于“回车”（carriage return）和“换行”（line feed）这两个概念的来历和区别。

在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打10个字符。但是它有一个问题，就是打完一行换行的时候，要用去0.2秒，正好可以打两个字符。要是在这0.2秒里面，又有新的字符传过来，那么这个字符将丢失。

于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做“回车”，告诉打字机把打印头定位在左边界；另一个叫做“换行”，告诉打字机把纸向下移一行（这句的意思是把纸向上拉，然后打印头就定位到了下一行），可以想象一下，这个打印头只能在一个固定的水平线上左右移动，而不能上下移动，我们通过移动纸来完成打印下一行。

不明白的我在youtube上找到一个这种打字机的演示视频，为了方便读者观看，我提供一个[下载地址](http://pan.baidu.com/share/link?shareid=428006&amp;uk=1493685990)。

后来，计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧。

Unix系统里，每行结尾只有"&lt;换行&gt;"，即"\n"；

Windows系统里面，每行结尾是"&lt;换行&gt;&lt;回车&gt;"，即"\n\r"；

Mac系统里，每行结尾是"&lt;回车&gt;"，不过mac基于unix，所以换行也应该是可以的。

一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。这个如果你在windows下使用vim也会发现这个情况

用C++来说明

如：
<pre class="lang:default decode:true">int main() 
{
   cout &lt;&lt; "leaver.me" &lt;&lt; "\r" &lt;&lt; "bystander" ;
   return 0;
}</pre>
&nbsp;

最后只显示 bystander 而 leaver.me 背覆盖了

\n 是换行，系统会将其替换成回车＋换行 把光标 先移到 行首 然后换到下一行 也就是 下一行的行首拉
<pre class="lang:default decode:true">int main() 
{
   cout &lt;&lt; "leaver.me" &lt;&lt; "\n" &lt;&lt; "bystander" ;
   return 0;
}</pre>
则 显示

leaver.me

bystander

一句话，这看起来是一个历史遗留问题......