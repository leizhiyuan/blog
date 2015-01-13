title: "[译]Unix sed实用教程第一篇--向文件中增加一行"
tags:
  - linux
  - shell
id: 3169
categories:
  - 我的翻译
date: 2013-08-09 14:56:29
---

Unix sed实用教程第一讲，本系列第一篇，有任何问题欢迎留言讨论。

sed 是unix中最重要的编辑器之一，注意，有之一..支持多种编辑任务，本文将实现题目的功能实例

假定我们有一额文本文件，叫做empFile，包含了员工名字和员工id，如下：
<pre class="lang:default decode:true">Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005</pre>
<span style="color: #3366ff;">1.如何通过sed给文件添加标题行-“Employee, EmpId”</span>
<pre class="lang:default decode:true">$ sed  '1i Employee, EmpId'  empFile
Employee, EmpId
Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005</pre>
解释：数字1，是说只对第一行执行操作，i代表在insert（熟悉vim的同学应该知道，i会在当前字符的前面插入，a是在后面插入），因此，1i就表示在将Employee, EmpId插入到第一行之前，

然后，有了标题行的文件仅仅会输出到标准输出，源文件内容并不会改变，如果需要更新源文件，可以使用重定向输出到一个临时文件，然后移动到原始文件。如果Unix系统的sed是GUN版本的，sed会有一个-i选项，可以直接实现更新源文件，（如何查看版本，终端下输入sed --version即可看到）下面先执行，再查看文件，发现已经多了标题行了
<pre class="lang:default decode:true">$ sed -i '1i Employee, EmpId' empFile
$ cat empFile
Employee, EmpId
Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005</pre>
<span style="color: #3366ff;">2.如何在标题行之后，也就是原始第一行之前添加一行横线--“-----”</span>
<pre class="lang:default decode:true">$ sed -i '1a ---------------'  empFile
$ cat empFile
Employee, EmpId
---------------
Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005</pre>
同1，中，1表示第一行，a表示append（附加），也就是说当读入第一行的时候在其之后添加一行，如果你使用2i作为命令也是正确的，就是指当读入第二行的时候，在其之前插入一行。

<span style="color: #3366ff;">3.如何在文件尾部添加一行</span>
<pre class="lang:default decode:true">$ sed -i '$a ---------------' empFile
$ cat empFile
Employee, EmpId
---------------
Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005
---------------</pre>
为了在文件尾部插入一行，如果使用之前的方法就需要知道总共有多少行，而$符号则直接指明了最后一行，因此$a表示在读入最后一行的时候，在后面插入一行

<span style="color: #3366ff;">4.如何在指定的记录之后插入一条新纪录</span>

假定我们的例子文件的内容现在是：
<pre class="lang:default decode:true">Employee, EmpId
---------------
Hilesh, 1001
Harshal, 1004
Keyur, 1005
---------------</pre>
如果我想在Hilesh这个员工之后插入Bharti员工的信息，我这样做：
<pre class="lang:default decode:true">$ sed -i '/Hilesh/a Bharti, 1002' empFile
$ cat empFile
Employee, EmpId
---------------
Hilesh, 1001
Bharti, 1002
Harshal, 1004
Keyur, 1005
---------------</pre>
注意看，我们这里已经不再使用数字或者其他表示行号的标识了，我们使用了一个模式（了解过[正则表达式](http://zh.wikipedia.org/zh/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)的朋友会比较熟悉，可以理解为某种规则- /Hilesh/a 这个命令表示对于每一行读入的内容，如果发现 /Hilesh/这个匹配，在该行之后插入一行，也就是说如果文件里有两行都是Hilesh员工，那么执行完上面的命令，将会附加两行内容，这里可以想想[sed的工作模式](http://leaver.me/archives/3162.html)，对每一行执行命令条件检测，发现匹配，就执行。

5.如何在指定记录之前插入记录呢，比如，我想在Harshal这条记录之前插入Aparna记录呢？
<pre class="lang:default decode:true">$ sed -i '/Harshal/i Aparna, 1003' empFile
$ cat empFile
Employee, EmpId
---------------
Hilesh, 1001
Bharti, 1002
Aparna, 1003
Harshal, 1004
Keyur, 1005
---------------</pre>
&nbsp;