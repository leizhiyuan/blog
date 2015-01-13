title: YAXLib---- XML序列化神器
tags:
  - C＃
  - 大学
  - 学习
  - 编程
  - 翻译
  - 软件
id: 1848
categories:
  - 我的翻译
date: 2012-10-05 16:24:31
---

今天早上翻译了[Yet-Another-XML-Serialization-Library-for-the-NET](http://www.codeproject.com/Articles/34045/Yet-Another-XML-Serialization-Library-for-the-NET)，刚开始以为很短。翻译着发现不对。。然后你不逼你自己。怎么知道自己做不到。于是。将近4个小时把30页的文档翻译完了。因为文章很长。所以本文只列出前两部分。我把翻译好的做成了pdf，

文档下载：[XML序列化神器](http://pan.baidu.com/share/link?shareid=74284&amp;uk=1493685990)

## 1 介绍

在本文中，会把要提到的XML序列化库叫做YAXLib，我们知道。.Net 还是提供了一些序列化功能的，尤其是XmlSerializer，该类被程序员广泛使用用来序列化对象成XML，当然，反序列化也是可以的。我认为XmlSerializer类的问题有几下几点

1.  程序员不能自由的选择生成的xml的结构
2.  不支持序列化一些集合类，比如Dictionary&lt;,&gt; 或者IEnumerable&lt;&gt;的属性
3.  当反序列化的时候，如果缺失了一些域，则反序列化失败，这就使得用来存储一ixekeyi被用户编辑的配置文件变得不合适了。
&nbsp;

## 2 为什么使用YAXLib

YAXLib解决上述问题的特点

1.  程序员可以决定xml文件的结构，一个属性可以是一个子元素，或者是其他属性的属性，或者是一个在类中没有对应属性的元素。
2.  集合类也可以被序列化成一个逗号分隔（也可以是其他任何分隔符）的数据项列表，而且。为Dictionary&lt;,&gt;对象实现了一些特殊的格式化功能，这样，使得程序员可以完全控制生成的xml文件的结构
3.  他支持System.Collections.Generic 命名空间中的所有泛型集合类（像`Dictionary`, `HashSet`, `LinkedList`, `List`, `Queue`,`SortedDictionary`, `SortedList`, 和 `Stack`） 和在`System.Collections`  命名空间中的非泛型集合类（ `ArrayList`, `BitArray`, `Hashtable`, `Queue`, `SortedList`, 和 `Stack`）非泛型集合类可以包含多种不同的对象，而且，库还支持序列化和反序列化一维，多维，不规则的数组。
4.  支持通过对基类/接口的引用，实现对一些对象集合的序列化和反序列化。
5.  支持多级反序列化
6.  程序员可以为生成的xml提供注释
7.  当进行反序列化的时候，程序员可以选择性对于那些与类的属性相关，但没有出现在xml文件中的数据应该如何处理。这种情况下可以看错是一个错误，然后类库抛出一些异常，或者记录错误，或者可以被看成一个警告，然后用程序员预定义的值赋给对应的属性，而且，程序可以可以选择忽略这个问题，相关的异常将既不抛出也不作任何记录。请查看保留空引用标识那一节 看看什么时候可以忽略孤立的数据也许对你有帮助
8.  程序员可以自己选择错误处理规则，对于数据敏感的应用程序，程序员可以选择在任何异常的情况下，库都应该抛出并且记录异常，对于其他的一些情况（比如要求不那么高的配置文件的存储），程序员可以选择把异常仅仅看成一个警告，仅仅记录一下，让程序的其他部分继续运行。
文档下载：[XML序列化神器](http://pan.baidu.com/share/link?shareid=74284&amp;uk=1493685990)

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！