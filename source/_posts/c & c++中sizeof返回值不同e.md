title: "c & c++中sizeof返回值不同?"
tags:
  - stackoverflow
  - 原创
  - 总结
  - 编程
  - 翻译
id: 702
categories:
  - 学习笔记
date: 2012-05-22 11:28:27
---

问题:
A character array is defined globally and a structure with same name is defined within a function.
一个字符数组被定义为全局变量，一个相同名字的结构体被定义在一个函数内部。

Why sizeof operator returns different values for c &amp; c++ ?
为什么sizeof操作符对于C和C++返回了不同的值呢？
<pre lang="c">char S[13];
void fun()
{
    struct S
    {
        int v;
    };
    int v1 = sizeof(S);
}
// returns 4 in C++ and 13 in C</pre>
答案：
Because in C++, the struct you defined is named S, while in C, 
因为在C++中，你定义的结构体的名称是S，而在C中，

it's named struct S (which is why you often see typedef struct used in C code). 
他叫做struct S（这也是为什么我们可以经常看到typedef struct 被用在C代码中）。

In C, to refer to the struct type, you need to say struct S. Therefore, sizeof(S) refers to the array.
在C中，引用一个结构类型的时候，你必须说struct S，因此，sizeof（S）调用的是数组S。

In C++, struct is unnecessary. So the local S hides the global S.
在C++中，struct这个字不是必需的，所以局部变量S隐藏了全局变量S。

If you were to change the code to the following, you would get the expected results:
如果你把代码改成下面的样子，你就能得到你期望的结果了。
<pre lang="c">
char S[13];
void fun()
{
    typedef struct _S
    {
        int v;
    } S;
    int v1 = sizeof(S);
}
</pre>

原文:[http://stackoverflow.com/q/10670261/764869](http://stackoverflow.com/q/10670261/764869)