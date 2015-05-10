title: 引用和指针(C++)
tags:
  - C++
  - 操作系统
  - 编程
  - 软件
id: 2976
categories:
  - 学习笔记
date: 2013-05-22 16:16:24
---

今天在整理收藏夹的时候，又看到了这两篇非常不错的文章，关于指针和引用的，我就不翻译了，文章很简单，不过把其中我觉得很有意思的两部分结合我的理解希望说的更清楚,假定你读这篇文章之前已经知道指针，但是不是很清楚其中的部分。

首先是关于指针的一个直观的一个认识.
<pre class="lang:default decode:true">#include &lt;iostream&gt;

int main()
{
  using namespace std;

// 声明并初始化指针.
  unsigned short int * pPointer = 0;
// 定义一个unsigned short int 变量 值为35698
  unsigned short int twoInt = 35698;
// 定义一个unsigned short int 变量 值为 77
  unsigned short int oneInt = 77;
// 使用&amp;操作符将twoInt的地址赋给指针
  pPointer = &amp;twoInt;
// pPointer 现在的值就是twoInt的地址了

// 打印
  cout &lt;&lt; "pPointer的内存地址:\t\t" &lt;&lt; &amp;pPointer &lt;&lt; endl;
  cout &lt;&lt; "oneInt的内存地址:\t" &lt;&lt; &amp;oneInt &lt;&lt; "\t整数值:\t" &lt;&lt; oneInt &lt;&lt; endl;
  cout &lt;&lt; "twoInt的内存地址:\t" &lt;&lt; &amp;twoInt &lt;&lt; "\t整数值:\t" &lt;&lt; twoInt &lt;&lt; endl;
  cout &lt;&lt; "pPointer所指向的地址(也就是pPoint的值):\t" &lt;&lt; pPointer &lt;&lt; "\t整数值:\t" &lt;&lt; *pPointer &lt;&lt; endl;

return 0;
}</pre>
上面这段代码，声明了一个指针，和两个整数，指针指向第一个整数，然后输出结果（每个人的输出结果不一样）是：
<pre class="lang:default decode:true">pPointer的内存地址:                   0xbff43314
oneInt的内存地址:                     0xbff43318       整数值:  77
twoInt的内存地址:                     0xbff4331a       整数值:  35698
pPointer所指向的地址(也就是pPoint的值): 0xbff4331a       整数值:  35698</pre>
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/ef97273d51f6843eb062463a3091a8edb177b30e.png)](http://leaverimage.b0.upaiyun.com/36294_o.png)
=======
[![](/images/ef97273d51f6843eb062463a3091a8edb177b30e.png)](http://leaverimage.b0.upaiyun.com/36294_o.png)
>>>>>>> 换电脑之后重新备份

这张图是这几个变量的内存分布图，上面一排是内存地址编号，正对我们的是该处的值

指针pPointer这个变量从最左边0xbff43314这个内存位置开始，占用了四个字节，这个一个指针占四个字节，大家都知道的，这个变量的值是一个地址，也就是他的指向地址。

pPointer 所指向的地址呢，是一个short int 类型的值所在的位置，在这里就是我们的twoint，twoint在内存中呢，是从0xbff4331a开始的，也就是说，我们的pPointer指向了twoint所在的地址，

注意，看一下左边四个方块前面的二进制数，转换成16进制就是0xbff4331a了

因此，取指针所指向的值就是35698了。指针本身的值则是0xbff4331a，指针所在的位置是0xbff43314。

参考文章：[C++ : Understanding pointers](http://linuxconfig.org/c-understanding-pointers)

&nbsp;

另一个呢是关于引用的实现原理

大家都知道，引用就是别名，比如下面这段代码
<pre class="lang:default decode:true">#include &lt;iostream&gt;
using namespace std;

int main() {
   int number = 88;          // 定义一个int变量
   int &amp; refNumber = number; // 声明并初始化一个引用
                             // 现在他们都表示了同一个值

   cout &lt;&lt; number &lt;&lt; endl;    // 打印number的值 (88)
   cout &lt;&lt; refNumber &lt;&lt; endl; // 打印引用的值 (88)

   refNumber = 99;            // 给refNumber重新赋一个值
   cout &lt;&lt; refNumber &lt;&lt; endl;
   cout &lt;&lt; number &lt;&lt; endl;    // number的值也就变成了 (99)

   number = 55;               // 重新给number赋一个值
   cout &lt;&lt; number &lt;&lt; endl;
   cout &lt;&lt; refNumber &lt;&lt; endl; // refNumber也就变成了(55)
}</pre>
输出结果肯定一目了然，其中的原理想过没

其实，引用和指针的工作原理类似，引用变量存储了其引用变量的地址，看图

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/4bfa9483e34045214dcefa4a6a383a8d189cc7fa.png)](http://leaverimage.b0.upaiyun.com/36295_o.png)
=======
[![](/images/4bfa9483e34045214dcefa4a6a383a8d189cc7fa.png)](http://leaverimage.b0.upaiyun.com/36295_o.png)
>>>>>>> 换电脑之后重新备份

&nbsp;

refNumber这个引用变量在内存中的值就是number这个被引用变量的地址.但和指针不同的是，引用是对一个地址的命名常量，因此，必须在声明引用的时候初始化。并且取值的时候，也不需要像指针一样加*，而是当作一个普通变量，直接用就行了。

参考文章：[Pointers, References and Dynamic Memory Allocation](http://www.ntu.edu.sg/home/ehchua/programming/cpp/cp4_PointerReference.html)

&nbsp;

&nbsp;