---
title: "C#中的字符串驻留"
tags:
  - C＃
  - 大学
  - 总结
  - 笔记
  - 软件
id: 1146
categories:
  - 学习笔记
date: 2012-07-08 20:57:11
---

字符串string可以理解为char[]，他是一个引用类型。

### 字符串创建

> 操作上类似于int，char等类型，直接进行赋值，string str="bystander";虽然string 是个类，但是如果你天真的使用
> <pre class="lang:c# decode:true ">string str=new string("bystander");</pre>
> 来构造，会导致一个编译错误。因为System.String只提供了数个接受Char*，Char[]类型参数的构造函数。
> 所以只能像下面这样使用，
> <pre class="lang:c# decode:true ">Char[] cs={'b','y','e'};
> String str=new String(cs);</pre>
> 看出来了吧，很麻烦的。所以一般还是使用第一种。

### 字符串恒定性

> 恒定性(Immutability)，是指字符串一经创建，就不可改变，这是String最为重要的特性之一。具体来说，就是字符串一旦创建，就会在托管堆上分配一块连续的内存空间，我们对其的任何改变都不会影响到原有的String对象，而是重新创建的新的String对象。类似Insert，Substring，ToUpper都只是创建出了新的临时的字符串，会成为下次垃圾回收的目标。
> 好处：
> 
> *   保证了对String对象的任何操作不会改变源字符串。
> *   恒定性还意味着操作字符串不会出现线程同步问题
> *   恒定性一定程度上，成就了字符串驻留。

### 字符串驻留

> CLR维护一个表来存放字符串，该表叫做拘留表（或驻留池），他包含程序上以编程方式声明或创建的每一个唯一的字符串的引用，因此具有特定值的实例在系统中只有一个。如果将同一个字符串分配给多个变量，那么CLR就会向拘留池检索相同引用，并分配给变量。
> 
> 通过下面这个例子来说明：
> <pre class="lang:c# decode:true ">class StringInterning
> {
>      public static void Main()
>         { 
>             string strA = "bystander";
>             string strB = "bystander";
>             Console.WriteLine(ReferenceEquals(strA,strB));
>             string strC = "by";
>             string strD = strC+"stander";
>             Console.WriteLine(ReferenceEquals(strA,strD));
>             strD=String.Intern(strD);
>             Console.WriteLine(ReferenceEquals(strA,strD));
>         }
> }</pre>
> 猜猜答案是什么。。
> 正确答案是：True，False，True
> 为什么不是我们通常认为的那样呢。这就是因为字符串驻留了
> 
> ### 缘起
> 
> > String类型的特性恒定性，对字符串的任何操作都只会创建新的字符串，这会导致性能下降，而String又用的很频繁，为此，CLR使用字符串驻留来解决这一问题。为此，CLR内部维护一个哈希表，来管理其创建的大部分string对象。其中Key为string本身，Value为分配给对应的string的内存地址。
> 
> ### 细节
> 
> > <pre class="lang:c# decode:true ">string strA = "bystander";</pre>
> > 　　CLR初始化时，创建一个空的哈希表，当JIT编译方法的时候，会首先在哈希表中查找每一个字符串常量，显然找不到任何"bystander"变量，因此会在托管堆中创建一个新的string对系那个strA，并更新哈希表，Key被赋值为"bystander",Value被赋值为strA的引用.也就是Value内保留了"bystander"在托管堆中的引用地址.
> > <pre class="lang:c# decode:true ">string strB = "bystander";</pre>
> > 　　接着,JIT根据"bystander"查找哈希表,结果找到了,所以JIT不做操作,只把找到的key对应的Value值赋给了strB对象.因此,第一个输出为true,引用相等.
> > <pre class="lang:c# decode:true "> string strC = "by";
> >  string strD = strC+"stander";</pre>
> > 　　同样,JIT向哈希表中添加了Key为"by",Value为托管堆上"by"的地址.返回strC对象.但是注意,strD不同,JIT不检测,因为strD他是动态生成的.这样的字符串不会被添加到哈希表中进行维护,而是在托管堆中直接分配,所以第二个Console输出False.
> > 
> > 　　对于第三个,我们首先看看Intern方法和IsInterned方法,对于动态生成的字符串,因为没有添加到CLR维护的哈希表,所以字符串驻留机制对其失效,但是可以手工开启,来实现高效的比较字符串相等.
> > <pre class="lang:c# decode:true ">public static string Intern(string str)
> > public static string IsInterned(string str)</pre>
> > 　　两者的机制都是去哈希表中查找是否存在str字符串,找到的话也都返回对str的引用,不同的是当哈希表中没有str的话,IsInterned返回null,而Intern将把这个字符串添加到哈希表,并返回引用.注意,IsInterned返回非null并不代表两个字符串引用了相同的地址.
> > 所以
> > <pre class="lang:c# decode:true ">strD=String.Intern(strD);
> > Console.WriteLine(ReferenceEquals(strA,strD));</pre>
> > 就很好理解了.