---
title: "[译]Unix sed实用教程第三篇–读写文件"
tags:
  - linux
  - shell
id: 3176
categories:
  - 我的翻译
date: 2013-08-09 16:52:56
---

本文将展示如何将文件内容读入到sed输出，同时包含如何将一个文件的部分内容写入到另一文件

_**<span style="color: #3366ff;">一.文件读取</span>**_

假定有两个文件，file1和file2，内容分别如下：
<pre class="lang:default decode:true">$ cat file1 
1apple 
1banana 
1mango</pre>
<pre class="lang:default decode:true">$ cat file2 
2orange 
2strawberry</pre>
sed有两个选项用来读写文件
<pre class="lang:default decode:true">r filename : 读取filename指定的文件内容
w filename : 将内容写入filename指定的文件</pre>
看例子：

<span style="color: #3366ff;">1.在file1的每一行读完之后读取file2的内容</span>
<pre class="lang:default decode:true">$ sed 'r file2' file1
1apple
2orange
2strawberry
1banana
2orange
2strawberry
1mango
2orange
2strawberry</pre>
r file2读取file2的所有内容，因此r之前没有知道那个行号或匹配，因此有了上面的输出，记住，sed的工作机制，每次读file1的一行，然后执行命令

<span style="color: #3366ff;">2.如何在读取了file1的第一行之后将file2读入</span>
<pre class="lang:default decode:true">$ sed '1r file2' file1
1apple
2orange
2strawberry
1banana
1mango</pre>
r前面加个1就行了

<span style="color: #3366ff;">3.当file1某行匹配了模式之后，读入file2</span>
<pre class="lang:default decode:true">$ sed '/banana/r file2' file1
1apple
1banana
2orange
2strawberry
1mango</pre>
sed逐行读入file1，然后判断该行是否匹配banana，如果匹配，就读入file2

<span style="color: #3366ff;">4.当file1读取完成后读入file2，其实就是合并两个文件</span>
<pre class="lang:default decode:true">$ sed '$r file2' file1
1apple
1banana
1mango
2orange
2strawberry</pre>
这里只是演示一下，其实cat file1 file2就可以完成合并

<span style="color: #3366ff;">_**二.文件写入**_</span>

使用一个file1文件，内容如下：
<pre class="lang:default decode:true">$ cat file1
apple
banana
mango
orange
strawberry</pre>
<span style="color: #3366ff;"> 1.将file1的2-4行写入到file2</span>
<pre class="lang:default decode:true">$ sed -n '2,4w file2' file1</pre>
2,4w就是写2-4行的意思，那-n呢？默认情况下sed会把读入的文件处理的结果输出到标准输出，也就是终端，而为了不使用默认输出，-n就派上用场了，执行该命令终端不会有任何输出
<pre class="lang:default decode:true">$ cat file2
banana
mango
orange</pre>
查看file2内容，发现已经写入成功了

<span style="color: #3366ff;">2.从第三行开始全部写入file2</span>
<pre class="lang:default decode:true">$ sed -n '3,$w file2' file1

$ cat file2
mango
orange
strawberry</pre>
就不多解释了

<span style="color: #3366ff;">3.如果是用正则呢？</span>
<pre class="lang:default decode:true">$ sed -n '/apple/,/mango/w file2' file1

$ cat file2
apple
banana
mango</pre>
该命令将逐行读入file1，然后判断该行是否匹配apple，如果匹配，则作为起始行，然后继续读入，判断是否匹配mango，如果是，则作为终止行，然后将中间的内容写入到file2

&nbsp;