---
title: "[译]Unix sed实用教程第五篇–替换文件内容续"
tags:
  - linux
  - shell
id: 3183
categories:
  - 我的翻译
date: 2013-08-10 09:05:55
---

前面已经学习过[替换文件内容](http://leaver.me/archives/3174.html)了，本文我们学习一些更频繁使用的搜索替换操作.

示例文件使用：
<pre>$ cat file
RE01:EMP1:25:2500
RE02:EMP2:26:2650
RE03:EMP3:24:3500
RE04:EMP4:27:2900</pre>
<span style="color: #3366ff;">1.替换每行开始的两个字母，这里用XX来替换</span>
<pre>$ sed 's/^../XX/' file
XX01:EMP1:25:2500
XX02:EMP2:26:2650
XX03:EMP3:24:3500
XX04:EMP4:27:2900</pre>
s代表substitute，前面说过了，^用来匹配行开头，.表示任意一个字符，两个..就是你懂的，

其实，不要^符号也是可以的，因为默认就是从开头开始匹配，如下也可以实现
<pre>sed 's/../XX/' file</pre>
<span style="color: #3366ff;">2.删除每行开头的两个字符</span>
<pre>$ sed 's/^..//' file
01:EMP1:25:2500
02:EMP2:26:2650
03:EMP3:24:3500
04:EMP4:27:2900</pre>
看到没有，后两个斜线之间没有内容，也就是用空字符来替换开头的两个字符，就实现了删除

<span style="color: #3366ff;">3.要是想删除每行最后的两个字符呢</span>
<pre>$ sed 's/..$//' file
RE01:EMP1:25:25
RE02:EMP2:26:26
RE03:EMP3:24:35
RE04:EMP4:27:29</pre>
再次强调，$在不同的情况下表示不同的意思，这里匹配行尾，有时候也匹配文件尾部

<span style="color: #3366ff;">4.向每行末尾添加内容</span>
<pre>$ sed 's/$/.Rs/' file
RE01:EMP1:25:2500.Rs
RE02:EMP2:26:2650.Rs
RE03:EMP3:24:3500.Rs
RE04:EMP4:27:2900.Rs</pre>
这里，先匹配行尾，然后把".Rs"添加到行尾

<span style="color: #3366ff;">5.在每行开头添加空格</span>
<pre>$ sed 's/^/   /' file
   RE01:EMP1:25:Rs.2500
   RE02:EMP2:26:Rs.2650
   RE03:EMP3:24:Rs.3500
   RE04:EMP4:27:Rs.2900</pre>
还记得前面说过的么，sed默认不影响原始文件，要是想更新原始文件，请加-i参数
<pre>$ sed -i 's/^/   /' file
$ cat file
   RE01:EMP1:25:Rs.2500
   RE02:EMP2:26:Rs.2650
   RE03:EMP3:24:Rs.3500
   RE04:EMP4:27:Rs.2900</pre>
<span style="color: #3366ff;">6.移除开始的空格</span>
<pre>$ sed 's/^ *//' file
RE01:EMP1:25:2500
RE02:EMP2:26:2650
RE03:EMP3:24:3500
RE04:EMP4:27:2900</pre>
^匹配行首，然后是一个空格，然后是*，表示一个或多个空格嘛，然后替换为空字符

<span style="color: #3366ff;">7.移除行首和行尾的空格</span>
<pre>$ sed 's/^ *//; s/ *$//' file
RE01:EMP1:25:2500
RE02:EMP2:26:2650
RE03:EMP3:24:3500
RE04:EMP4:27:2900</pre>
不要怕，从分号处分开，就是两条命令啦，一个做行首的，一个做行尾的，前面说过的，可以使用-e参数来分开
<pre>sed -e 's/^ *//' -e 's/ *$//' file</pre>
<span style="color: #3366ff;">8.如何在一个字符串前后添加一些其他字符呢，可以用来字符串转义</span>
<pre>$ sed 's/.*/"&amp;"/' file
"RE01:EMP1:25:Rs.2500"
"RE02:EMP2:26:Rs.2650"
"RE03:EMP3:24:Rs.3500"
"RE04:EMP4:27:Rs.2900"</pre>
前两个斜线匹配了所有字符，也就是本行，然后后面两个斜线是替换的内容，注意里面的&amp;符号，之前也说过的，表示匹配到的字符，也就是本行了。所有就这样啦

<span style="color: #3366ff;">9.移除行首和行尾的一个字符（多个你也会的啦）</span>
<pre>$ sed 's/^.//;s/.$//' file
RE01:EMP1:25:2500
RE02:EMP2:26:2650
RE03:EMP3:24:3500
RE04:EMP4:27:2900</pre>
分开看看，你可以的

<span style="color: #3366ff;">10.删除第一个数字之前的所有字符</span>
<pre>$ sed 's/^[^0-9]*//' file
01:EMP1:25:2500
02:EMP2:26:2650
03:EMP3:24:3500
04:EMP4:27:2900</pre>
这条命令略复杂，请先回头看一下我们的示例文件，然后我们开始分析这个表达式，地一个^表示匹配行首，然后[^0-9]* 是一个典型的正则，匹配非数字的多个字符，简单说一下[0-9]表示匹配0,1,2,3,4,5,6,7,8,9任意一个字符，前面加个一个^，表示非，也就是不匹配这些数字，然后最后是一个*，表示匹配非数字的连续多个字符，有机会的话我会推荐一个正则系列，或者自己写一篇文章。这里就先这样理解吧。

那猜猜这个是干嘛的
<pre>sed 's/^[^a-zA-Z]*//' file</pre>
是的，你猜对了，删除开头的所有非字母字符。

<span style="color: #3366ff;">11.删除行尾的数字</span>
<pre>$ sed 's/[0-9]*$//' file
RE01:EMP1:25:
RE02:EMP2:26:
RE03:EMP3:24:
RE04:EMP4:27:</pre>
看看前面的你就知道了

<span style="color: #3366ff;">12.通过分隔符得到文件最后一列</span>
<pre>$ sed 's/.*://' file
2500
2650
3500
2900</pre>
可能你你预想的不一样，是不是，你可能认为会是第一列被删除，其他的列都在，不对的，sed是很贪心的，当我们指定'.*:'时，他会一直向后查找，尽量匹配更多，所以，就匹配到最后一个列之前了

<span style="color: #3366ff;">13.转换整行字符为小写</span>
<pre>$ sed 's/.*/\L&amp;/' file
re01:emp1:25:rs.2500
re02:emp2:26:rs.2650
re03:emp3:24:rs.3500
re04:emp4:27:rs.2900</pre>
\L是sed的一个命令，Lowercase嘛。\L之后的内容被毁转换，而&amp;表示前面匹配道德内容，所以就实现了转换

<span style="color: #3366ff;">14.转换大写</span>

\U你懂的，Uppercase
<pre>$ sed 's/.*/\U&amp;/' file
RE01:EMP1:25:RS.2500
RE02:EMP2:26:RS.2650
RE03:EMP3:24:RS.3500
RE04:EMP4:27:RS.2900</pre>