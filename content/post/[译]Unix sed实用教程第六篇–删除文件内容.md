---
title: "[译]Unix sed实用教程第六篇–删除文件内容"
tags:
  - linux
  - shell
id: 3186
categories:
  - 我的翻译
date: 2013-08-10 13:15:38
---

其实，删除和替换是由一些相同的，不过，这里我们单独列出来，通过25个例子穿插讲解sed删除文件的一些方法，使用的示例文件如下：
<pre class="lang:default decode:true">$ cat file
Cygwin
Unix
Linux
Solaris
AIX</pre>
<span style="color: #3366ff;">1.删除第一行</span>
<pre>$ sed '1d' file
Unix
Linux
Solaris
AIX</pre>
d就是删除，1就是指第1行，记得哦，这不会影响到源文件，一般，做删除的时候一般要加-i参数，前面说过了
<pre>sed -i '1d' file</pre>
<span style="color: #3366ff;">2.删除指定行，这里删除第3行</span>
<pre>$ sed '3d' file
Cygwin
Unix
Solaris
AIX</pre>
<span style="color: #3366ff;">3.删除最后一行</span>
<pre>$ sed '$d' file
Cygwin
Unix
Linux
Solaris</pre>
<span style="color: #3366ff;">4.删除范围行，这里删除2-4行</span>
<pre>$ sed '2,4d' file
Cygwin
AIX</pre>
<span style="color: #3366ff;">5.保留指定的行，这里保留2-4行</span>
<pre>$ sed '2,4!d' file
Unix
Linux
Solaris</pre>
！这个是对前面的2,4来操作，是非的意思，也就是不是2-4行的行，这样除去2-4行，其他的都删除了

<span style="color: #3366ff;">6.删除第一行和最后一行</span>
<pre>$ sed '1d;$d' file
Unix
Linux
Solaris</pre>
分号隔开两个命令，你懂的，也可以删除第二行和第三行等等. '2d;3d'

<span style="color: #3366ff;">7.删除以指定字符开头的行</span>
<pre>$ sed '/^L/d' file
Cygwin
Unix
Solaris
AIX</pre>
这里就把以L开头的Linux这一行删除了

<span style="color: #3366ff;">8.删除以指定字符结尾的行</span>
<pre>$ sed '/x$/d' file
Cygwin
Solaris
AIX</pre>
这里就删除了，可以看到AIX没有删除，unix区分大小写你懂的

<span style="color: #3366ff;">9.忽略大小写，都删除</span>
<pre>$ sed '/[xX]$/d' file
Cygwin
Solaris</pre>
[xX]匹配x或X，所以就成功了嘛

<span style="color: #3366ff;">10.删除文件中的空行</span>
<pre>$ sed '/^$/d' file
Cygwin
Unix
Linux
Solaris
AIX</pre>
^匹配开头，$匹配结果，中间啥都没有，这样就匹配了空行，但是注意哦，如果某一行全是空格，这个命令是不会删除这一行的。

<span style="color: #3366ff;">11.删除空行或是仅仅包含空格的行</span>
<pre>$ sed '/^ *$/d' file
Cygwin
Unix
Linux
Solaris
AIX</pre>
如果你看我前面的文章，这个就不用我说了吧，0个或多个空格就是匹配了所有的空行了

<span style="color: #3366ff;">12.删除完全是大写字母的行</span>
<pre>$ sed '/^[A-Z]*$/d' file
Cygwin
Unix
Linux
Solaris</pre>
[A-Z]就匹配了26个大写字母的任意一个

<span style="color: #3366ff;">13.删除包含Unix匹配的行</span>
<pre>$ sed '/Unix/d' file
Cygwin
Linux
Solaris
AIX</pre>
<span style="color: #3366ff;">14.删除不包含Unix的行</span>
<pre>$ sed '/Unix/!d' file
Unix</pre>
<span style="color: #3366ff;">15.删除包含Unix或Linux匹配的行</span>
<pre>$ sed '/Unix\|Linux/d' file
Cygwin
Solaris
AIX</pre>
这里要说的是|，这个本来是表示or，或的意思，但是在终端下，为了防止被认为是管道，所以要用\来转义

<span style="color: #3366ff;">16.删除从第一行到满足匹配的行</span>
<pre>$ sed '1,/Linux/d' file
Solaris
AIX</pre>
可以通过行号到模式，模式到行号等等组合来匹配

<span style="color: #3366ff;">17.删除从Linux匹配到末尾的行</span>
<pre>$ sed '/Linux/,$d' file
Cygwin
Unix</pre>
<span style="color: #3366ff;">18.如果最后一行匹配AIX就删除，否则就不删除</span>
<pre>$ sed '${/AIX/d;}' file
Cygwin
Unix
Linux
Solaris</pre>
$匹配了最后一行，也可以是行号，比如1，后面的大括号里就是条件和处理方式了，这样就在sed里实现了if条件了，再看一遍这个例子，熟悉一下这个规则

<span style="color: #3366ff;">19.所处最后一行如果满足两个匹配</span>
<pre>$ sed '${/AIX\|HPUX/d;}' file
Cygwin
Unix
Linux
Solaris</pre>
<span style="color: #3366ff;">20.如果1-4行满足Solaris匹配就删除</span>
<pre>$ sed '1,4{/Solaris/d;}' file
Cygwin
Unix
Linux
AIX</pre>
<span style="color: #3366ff;">21.删除包含Unix匹配的行，同时删除下一行</span>
<pre>$ sed '/Unix/{N;d;}' file
Cygwin
Solaris
AIX</pre>
N命令将下一行读入pattern space，什么。你忘了，看[开篇](http://leaver.me/archives/3162.html)去，这个命令就删除了两行

<span style="color: #3366ff;">22.删除包含Unix的下一行</span>
<pre>$ sed '/Unix/{N;s/\n.*//;}' file
Cygwin
Unix
Solaris
AIX</pre>
作者这里没有说清楚，我讲一下，N读入了下一行，然后这时候pattern space里就相当于两行并作了一行，中间有一个换行符，所以pattern space里现在就是：
<pre>Unix\nLinux</pre>
然后通过s命令匹配了\nLinux，并且替换为空，就ok了哦。

<span style="color: #3366ff;">23.删除包含Linux模式的行，也包含上一行</span>
<pre>$ sed -n '/Linux/{s/.*//;x;d;};x;p;${x;p;}' file | sed '/^$/d'
Cygwin
Solaris
AIX</pre>
少年，不要怕，我们一步步来

首先，要知道，为了删除某一匹配的前一行，我们需要将文件的每一行放入到hold space里，一旦发现匹配，我们就开始删除连个缓冲区的内容，其中pattern space存储了当前行，hold space 存储了前一行

而x;p;这是啥呢，这两个命令是对每一行都会执行的，x表示exchange，即是交换hold space 和pattern space中的内容，p则是print，打印pattern space中的内容，因此，每次当前行被存入hold space，然后打印出了前一行的内容

当/Linux/匹配之后，我们使用s/.//来清空当前行，也就是pattern space为空了，当前行相当于被删了，然后使用x和hold space来交换，这样，就导致hold space为空了，然后使用d来删除pattern space的内容（此时pattern space里是交换过来的前一行的内容），因此，匹配Linnx的当前行和前一行都被删除了，

然后呢${x;p;}表示如果最后一行还在hold space里，就把他换出来，打印出来。

而后面的管道之后的命令呢，是吧第一部分sed命令之后产生的空行给删掉。

再理解一下

<span style="color: #3366ff;">24.仅删除匹配Linux的前一行，不删除当前行</span>
<pre>$  sed -n '/Linux/{x;d;};1h;1!{x;p;};${x;p;}' file
Cygwin
Linux
Solaris
AIX</pre>
和23类似，当匹配到Linux的时候，hold space 存储了前一行的内容，所以我们交换一下他们，然后前一行就进入了pattern space，然后一个d，这样就把前一行删掉了。别急，还没完，这样，当前行就被留在hold space了。就无法被输出了

1h;1!{x;p;}  1h是说，如果将第1行移动到hold space，也就是1h只对第一行操作，而后面的1!{x;p;}是说对于其他的行执行x;p;命令，

<span style="color: #3366ff;">25.删除匹配模式的前后两行</span>
<pre>$ sed -n '/Linux/{N;s/.*//;x;d;};x;p;${x;p;}' file | sed '/^$/d'
Cygwin
AIX</pre>
有了前面两个例子，自己试试理解一下，有问题留言讨论吧。