---
title: "[译]Unix sed实用教程第七篇–输出文件内容(10 Demo)"
tags:
  - linux
  - shell
id: 3191
categories:
  - 我的翻译
date: 2013-08-11 16:27:31
---

之前已经学习过[选择性打印输出](http://leaver.me/archives/3179.html)了，本文将通过10个例子全面讲解文件输出的一些方法，主打p命令

首先看一下将使用的示例文件
<pre>$ cat file
AIX
Solaris
Unix
Linux
HPUX</pre>
<span style="color: #3366ff;">1.打印文件首行</span>
<pre>$ sed -n '1p' file
AIX</pre>
之前讲过了，-n取消默认的全部打印，p表示print，1就是行号了

<span style="color: #3366ff;">2.输出最后一行</span>
<pre>$ sed -n '$p' file
HPUX</pre>
<span style="color: #3366ff;">3.输出不匹配X的那些行</span>
<pre>$ sed -n '/X/!p' file
Solaris
Unix
Linux</pre>
也好理解，！表示非，就是说包含X匹配的不打印输出

<span style="color: #3366ff;">4.输出包含u/x的那些行</span>
<pre>$ sed -n '/[ux]/p' file
Unix
Linux</pre>
正则是强大的，这样就匹配了u/x

<span style="color: #3366ff;">5.输出以x/X结尾的那些行</span>
<pre>$ sed -n '/[xX]$/p' file
AIX
Unix
Linux
HPUX</pre>
这里$符号不再是匹配文件尾部，而是行尾，这是正则的规则

<span style="color: #3366ff;">6.输出以A/L开头的行</span>
<pre>$ sed -n '/^A\|^L/p' file
AIX
Linux</pre>
前面也说过，^匹配了行首，A表示A匹配，然而\|则是或者的意思，为什么要加\转义，是为了避免被解析成pipe管道，后面的就不解释了

<span style="color: #3366ff;">7.隔行打印</span>

n命令是输出当前行，然后读入下一行到pattern space的意思，这句命令是这么执行的，首先读入一行，然后通过n命令输出，然后通过n命令再读一行，然后把这行删除，就出现了隔行输出的效果

<span style="color: #3366ff;">8.如何两行输出，隔两行再输出</span>
<pre>$ sed  'n;n;N;d' file
AIX
Solaris
HPUX</pre>
n;n; 命令呢输出了前两行，然后读入第三行到pattern space，N命令则对如下一行并与第三行合并，然后d命令删除pattern space中的内容，于是三四行被清空，然后读入56行，继续重复。就这样。作者这里给出的例子不直观，我举个例子，我的文件内容是
<pre class="lang:default decode:true">$ cat test.txt
1
2
3
4
5
6
7
8
9</pre>
执行上面的命令后，会输出
<pre class="lang:default decode:true">1
2
5
6
9</pre>
明白了吧

<span style="color: #3366ff;">9.输出某个范围行内以X结果的那些行</span>
<pre>$ sed -n '/Unix/,${/X$/p;}' file
HPUX</pre>
这里首先制定了从/Unix/匹配开始到文件尾部的这些行，然后呢，在这些之间的行，如果以X结果，就输出。

<span style="color: #3366ff;">10.输出不包括开始和结尾的那些行</span>
<pre>$ sed -n '/Solaris/,/HPUX/{//!p;}' file
Unix
Linux</pre>
这个命令就会只输出/Solaris/和/HPUX/之间的行，不包括他们两个。

这里要说一下//这个，当匹配了Solaris的时候就进入了花括号，因为这个里面没有提供任何匹配，所以就考虑最后一次匹配模式，也就是相当于是考虑Solaris这一行，他不被打印，当到了HPUX这一匹配，//又代表了HPUX匹配，这一行也不打印。

Demo完了，你懂了么。