---
title: "[译]Unix sed实用教程第八篇–CSV文件操作"
tags:
  - linux
  - shell
id: 3194
categories:
  - 我的翻译
date: 2013-08-11 16:55:33
---

本文作为sed使用教程的最后一篇，将比较全面的讲解如何操作csv文件，csv文件通过逗号分隔

示例文件如下：
<pre>cat file
Solaris,25,11
Ubuntu,31,2
Fedora,21,3
LinuxMint,45,4
RedHat,12,5</pre>
<span style="color: #3366ff;">1.删除第一列</span>
<pre>$ sed 's/[^,]*,//' file
25,11
31,2
21,3
45,4
12,5</pre>
s开启替换模式，当^符号在中括号里的时候，就是非的意思，也就是说[^,]匹配了所有不是逗号的一个字符，然后后面的星号表示0个或多个，然后是一个逗号，也就是匹配"xxxx,"替换为空

<span style="color: #3366ff;">2.删除除过最后一列的其他所有</span>
<pre>$ sed 's/.*,//' file
11
2
3
4
5</pre>
sed先匹配任意多个字符，然后匹配最后一个“,”这就直接把前面的都匹配完了。替换为空即可。

<span style="color: #3366ff;">3.输出第一列</span>
<pre>$ sed 's/,.*//' file
Solaris
Ubuntu
Fedora
LinuxMint
RedHat</pre>
好理解把，先匹配第一列之后的逗号，然后是多个字符，然后替换为空

<span style="color: #3366ff;">4.删除第二列</span>
<pre>$ sed 's/,[^,]*,/,/' file
Solaris,11
Ubuntu,2
Fedora,3
LinuxMint,4
RedHat,5</pre>
先匹配第一列之后的逗号，然后匹配一个或多个非逗号字符，这样就匹配了第二列的内容，然后再匹配一个逗号，我简化一下，比如该列是1，2，3，4，那么这一个匹配就是",2,"，替换成一个逗号，就是1，3，4了

<span style="color: #3366ff;">5.输出第二列</span>
<pre>$ sed 's/[^,]*,\([^,]*\).*/\1/' file
25
31
21
45
12</pre>
我们可以分析前两个斜线之间的内容
<pre>[^,]*,\([^,]*\).*</pre>
两个右斜线转移了括号，所以括号不是简单的符号，而是正则里的组的意义，圆括号的作用是对字符进行分组，并保存匹配的文本。这里先匹配非逗号的多个字符，然后一个逗号，然后再匹配第二列，并且第二列的匹配用括号括起来，然后匹配其他列，然后里面用\1来引用这个括号的内容，如果前面有两个括号，就可以使用\1或\2这样，分别表示第一个括号或第二个括号的匹配

<span style="color: #3366ff;">6.输出最后一列是一位数字的行</span>
<pre>$ sed -n '/.*,[0-9]$/p' file
Ubuntu,31,2
Fedora,21,3
LinuxMint,45,4
RedHat,12,5</pre>
.*匹配了前面的所有字符，然后,匹配了最后一个逗号，sed的贪婪原则。。然后[0-9]匹配一个数字$表示行尾.ok

<span style="color: #3366ff;">7.给每一行自动添加行号</span>
<pre>$ sed = file | sed 'N;s/\n/ /'
1 Solaris,25,11
2 Ubuntu,31,2
3 Fedora,21,3
4 LinuxMint,45,4
5 RedHat,12,5</pre>
这个和cat -n file的效果是一样的。awk也可以很简单的做，这里使用一个=命令会在每一行之前添加一个行号，也就是是，如果执行
<pre class="lang:default decode:true">sed = file</pre>
文件会是：
<pre>1 
Solaris,25,11
2 
Ubuntu,31,2
3 
Fedora,21,3
4 
LinuxMint,45,4
5 
RedHat,12,5</pre>
然后我们通过管道再执行
<pre>sed 'N;s/\n/ /'</pre>
N表示读入并合并下一行，然后将两行之间的换行符替换为空即可了

<span style="color: #3366ff;">8.如果第一列是Ubuntu，就把最后一列替换成99</span>
<pre>$ sed 's/\(Ubuntu\)\(,.*,\).*/\1\299/' file
Solaris,25,11
Ubuntu,31,99
Fedora,21,3
LinuxMint,45,4
RedHat,12,5</pre>
中间的
<pre>\(Ubuntu\)\(,.*,\).*</pre>
匹配“Ubuntu,任意个字符,任意个字符”,也就是将行分成几组，将本行替换成第一组的内容第一组的内容，第二组的内容，但最后的一列被替换成99，就这样。

<span style="color: #3366ff;">9.如果第一列是RedHat就删除第二列</span>
<pre>$ sed 's/\(RedHat,\)[^,]*\(.*\)/\1\2/' file
Solaris,25,11
Ubuntu,31,2
Fedora,21,3
LinuxMint,45,4
RedHat,,5</pre>
也好理解嘛。将要保留的用括号分组，然后用\1和\2分别引用第一组和第二组，然后中间的匹配就被删除了

<span style="color: #3366ff;">10.在末尾插入一列</span>
<pre>$ sed 's/.*/&amp;,A/' file
Solaris,25,11,A
Ubuntu,31,2,A
Fedora,21,3,A
LinuxMint,45,4,A
RedHat,12,5,A</pre>
正则首先匹配了整行字符，然后使用&amp;来引用该匹配，然后添加了一个逗号和一个A

11.在头部添加一列
<pre>$ sed 's/.*/A,&amp;/' file
A,Solaris,25,11
A,Ubuntu,31,2
A,Fedora,21,3
A,LinuxMint,45,4
A,RedHat,12,5</pre>
很简单不是么...

比预计的要快，两天翻译完了.体会到了理解原理对学习sed的好处，希望对想学的人有帮助.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;