title: "[译]Unix sed实用教程第四篇–选择性打印"
tags:
  - linux
  - shell
id: 3179
categories:
  - 我的翻译
date: 2013-08-10 08:39:56
---

本文，我们将会学习如何选择性的打印（其实，这里的打印是print，也就是输出到标准输出的意思），用到的示例文件是：
<pre class="lang:default decode:true">$ cat file 
Gmail 10 
Yahoo 20 
Redif 18</pre>
<span style="color: #3366ff;">1.打印所有内容</span>
<pre class="lang:default decode:true">$ sed '' file
Gmail 10
Yahoo 20
Redif 18</pre>
一对单引号，没有任何参数即可

<span style="color: #3366ff;">2.如何打印包含Gmail的那一行.(grep也可以实现这个功能)</span>
<pre>$ sed '/Gmail/p' file
Gmail 10
Gmail 10
Yahoo 20
Redif 18</pre>
在斜线里面，我们指定正则匹配模式，p的意思呢，就是print，打印的意思，也就是打印包含Gmail这一行，但是我们看到Gmail打印了两次，为什么，因为sed的默认行为是在解析完一行之后就把他输出出来，也就是对于Gmail这一行，先执行p解析，解析完成后再默认打印一次，就打印了两次，而其他的，没有命令解析，直接读入完成后输出即可。

如果得到期望的结果呢？
<pre>$ sed -n '/Gmail/p' file
Gmail 10</pre>
-n参数会取消sed的默认打印行为，所以就ok了

<span style="color: #3366ff;">3.删除包含Gmail的那一行。（grep -v也有同样的效果）</span>
<pre>$ sed  '/Gmail/d' file
Yahoo 20
Redif 18</pre>
d就是delete的意思，不多解释，

同样，要想删除某一指定的行
<pre>$ sed '1d' file
Yahoo 20
Redif 18</pre>
<span style="color: #3366ff;">4.打印直到模式匹配，这里我们从头一直打印到Yahoo</span>
<pre>$ sed  '/Yahoo/q' file
Gmail 10
Yahoo 20</pre>
q就是quit的意思，这条命令就是对于前面的行都没啥可解析的，执行默认的打印即可，一到碰到Yahoo这一行，打印完成，就停止，退出，因此，就是上面的了

打印某一指定范围行

到此，我们一直在学习基于一个条件取一行或多行，现在，我们来学习打印指定范围行

使用的示例文件如下：
<pre>$ cat file
Gmail 10
Yahoo 20
Redif 18
Inbox 15
Live  23
Hotml 09</pre>
<span style="color: #3366ff;">5.打印前三行</span>
<pre>$ sed -n '1,3p' file
Gmail 10
Yahoo 20
Redif 18</pre>
-n先取消默认打印，然后1,3指定行范围，p表示打印，你想一下我们前面的q命令，就知道下面这个命令效果是一样的
<pre>$ sed '3q' file
Gmail 10
Yahoo 20
Redif 18</pre>
执行默认打印，到第三行的时候退出

<span style="color: #3366ff;">6.通过模式指定范围，这里我打印从Yahoo到Live之间的行，包括本身</span>
<pre>$ sed -n '/Yahoo/,/Live/p' file
Yahoo 20
Redif 18
Inbox 15
Live  23</pre>
不用多解释了

<span style="color: #3366ff;">7.从指定模式到文件尾部，这里是从Redif到文件尾</span>
<pre>$ sed -n '/Redif/,$p' file
Redif 18
Inbox 15
Live  23
Hotml 09</pre>
回忆一下$符号吧.少年

<span style="color: #3366ff;">8.从开头到匹配模式</span>
<pre>$ sed -n '1,/Inbox/p' file
Gmail 10
Yahoo 20
Redif 18
Inbox 15</pre>
相信你懂的...

对于数字不需要斜线包围，对于匹配需要斜线.就这样..

&nbsp;

&nbsp;