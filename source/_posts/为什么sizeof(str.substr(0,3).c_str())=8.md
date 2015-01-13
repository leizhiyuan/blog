title: "为什么sizeof(str.substr(0,3).c_str())=8?"
tags:
  - 原创
  - 开发
  - 编程
  - 翻译
id: 677
categories:
  - 学习笔记
date: 2012-05-21 06:59:09
---

问题：
`string str = "abcdefgdcb";
cout &lt; &lt; sizeof(str.substr(0,3).c_str());`
For some reason, the above string is giving me 8\. I assumed c_str() returns a null string,
由于某些原因，上面的这个字符串得到的结果是8，我估计c_str()返回了一个null，

and sizeof uses the null to determine the size of the string.
并且sizeof函数使用这个null来定义这个字符串的大小。

答案：
Because sizeof doesn't give you the length of a string,
因为sizeof给你的不是一个字符串的长度，

it gives you the size of the type (const char * in this case). Try strlen.
他给你的是这个类型的大小（这种情况下的类型是c_str()返回的const char*类型），想要得到正确的结果，试试strlen函数吧。

On your system, sizeof (const char *) == 8, like any other pointer.
在你的系统上，sizeof(const char*)=8,和其他所有的指针类型一样。

8 is the size of a pointer on your machine (64-bit)
8是在你的64位电脑上一个指针的大小

There's your problem. sizeof tells you the size of a variable,
别乱假设，sizeof告诉你一个变量的大小，

which has nothing to do with the value inside the variable, ever.
他不会进入变量里面对变量做任何改变的。。永远不会。

问题:[http://stackoverflow.com/q/10668764/764869](http://stackoverflow.com/q/10668764/764869)