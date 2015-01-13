title: "[译]Unix sed实用教程第二篇–替换文件内容"
tags:
  - linux
  - shell
id: 3174
categories:
  - 我的翻译
date: 2013-08-09 16:24:12
---

上一节中-[Unix sed实用教程第一篇–向文件中增加一行](http://leaver.me/archives/3169.html) 学习了添加文件，本节讲解数据内容替换.

本节将使用sample1.txt文件作为示例，文件内容如下，都是些水果..：
<pre class="lang:default decode:true">apple 
orange 
banana 
pappaya</pre>
1.向每一行的开头添加内容，这里我们添加“Fruit：”
<pre class="lang:default decode:true">$ sed 's/^/Fruit: /' sample1.txt 
Fruit: apple 
Fruit: orange 
Fruit: banana 
Fruit: pappaya</pre>
解析：s代表substitution，也就是替换，s之后是要替换/匹配的内容，斜线/用来分隔s以及要替换的原始内容还有要替换的最终内容，而'^'符号是说一个正则，用来匹配每一行的开头，匹配成功后在开头加上'Fruit:'。

2.向每一行的行尾添加内容
<pre class="lang:default decode:true">$ sed 's/$/ Fruit/' sample1.txt 
apple Fruit 
orange Fruit 
banana Fruit 
pappaya Fruit</pre>
注意，这里的$和上一节的$符号表示的意义不同，这里则是表示行尾.

3.如何替换指定的字符，这里将小写a替换成大写A
<pre class="lang:default decode:true">$ sed 's/a/A/' sample1.txt 
Apple 
orAnge 
bAnana 
pAppaya</pre>
注意，仅仅将每一行的第一个a替换了，不是所有，本例表示替换单个字符，你可以替换一个单词都是可以的.

4.如何替换行内所有的字符，用A替换a
<pre class="lang:default decode:true">$ sed 's/a/A/g' sample1.txt 
Apple 
orAnge 
bAnAnA 
pAppAyA</pre>
注意，只是加了一个g选项，g为global的简写，就是全局，全部的意思。

5.如何替换第二次出现的a?
<pre class="lang:default decode:true">$ sed 's/a/A/2' sample1.txt 
apple 
orange 
banAna 
pappAya</pre>
不使用g，而是使用数字来表示行内第几次出现的a，结果如上

6.如何替换第二次之后的所有a呢？
<pre class="lang:default decode:true">$ sed 's/a/A/2g' sample1.txt 
apple 
orange 
banAnA 
pappAyA</pre>
很好理解对吧。

7.如果只想替换第三行的a呢？
<pre class="lang:default decode:true">$ sed '3s/a/A/g' sample1.txt 
apple 
orange 
bAnAnA 
pappaya</pre>
回想一下第一节，在执行命令之前，会判断当前address是否满足条件，3就是地址

8.想替换一个范围行内的数据呢
<pre class="lang:default decode:true">$ sed '1,3s/a/A/g' sample1.txt 
Apple 
orAnge 
bAnAnA 
pappaya</pre>
逗号隔开，即可

9.如何替换整行呢？比如用apple is a Fruit替换apple
<pre class="lang:default decode:true">$ sed 's/.*/&amp; is a Fruit/' sample1.txt 
apple is a Fruit 
orange is a Fruit 
banana is a Fruit 
pappaya is a Fruit</pre>
这里‘&amp;’符号标识了模式匹配到的内容，而.*匹配了正行，.表示任意字符，*表示一个或多个，也就是匹配了整行,&amp;因此就是整行内容，用来重命名一组文件的时候非常有用.

10.如何进行多个替换，比如用A替换a，用P替换p
<pre class="lang:default decode:true">$ sed 's/a/A/g; s/p/P/g' sample1.txt 
APPle 
orAnge 
bAnAnA 
PAPPAyA</pre>
也就是用分号分开即可。或者也可以通过-e参数来做
<pre>$ sed -e 's/a/A/g' -e 's/p/P/g' sample1.txt 
APPle 
orAnge 
bAnAnA 
PAPPAyA</pre>
-e 选项就是当需要替换多个的时候来用的。

另外，要是替换的太多。也可以通过右斜线来分成多行
<pre class="lang:default decode:true">$ sed -e 's/a/A/g' \ &gt; -e 's/p/P/g' sample1.txt 
APPle 
orAnge 
bAnAnA 
PAPPAyA</pre>
相信通过本文实例分析，平时的文件内容替换就很简单了