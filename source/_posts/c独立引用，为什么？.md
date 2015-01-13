title: C++ 独立引用，为什么？
tags:
  - 总结
  - 编程
  - 翻译
id: 716
categories:
  - 学习笔记
date: 2012-05-23 07:25:04
---

问题：

I read in some good C++ tutorial that independent references do exist, and act like aliasing.
我读了一些好的C++文章，发现独立引用确实存在，并且很像别名。
But... I wonder what it is made for.
但是，，，我想知道他适用于什么情况。
Why should one want to use aliasing.Besides, some piece of code that is not clear to me:
为什么有人想使用别名呢，另外，下面这段代码我不太清楚。
<pre class="lang:c++ decode:true">int a;
int &amp;ref = a; // independent reference
int b = 19;
ref = b;
cout &lt;&lt; a &lt;&lt; " " &lt;&lt; ref &lt;&lt; "\n";
ref--;
cout &lt;&lt; a &lt;&lt; " " &lt;&lt; ref &lt;&lt; "\n";</pre>
<span style="font-family: monospace;">
</span>

First, ref is a 'reference' to a.I understand from second line of code that address for ref (hence the ampershead) is
首先，ref是一个对a的引用，从第二行代码那我认为ref的地址也就是a，（括号里面不清楚什么意思。貌似是作者写错了）

a. Then, integer ref is assigned the value of b (19). First cout returns a and ref, both equal to 19.
然后呢，整数ref被b（19）被赋值，第一次输出a和ref，都是19，

Why? Isn't integer a the address for ref? Then, decrements ref, and last cout gives two times 18\. a and ref where decremented.
为什么，难道整数a不是给ref的地址？然后ref自减，最后输出了2个18，a和ref都减少了。

Only strange possible interpretation of mystery: here int&amp;; is a type in itself, 'independent reference to an integer',
唯一可能的解释就是：在这里，int&amp; 是一个类型，表示对一个整数的独立引用。

and this type means aliasing. Then whatever you do to ref, the same is done to a.
并且这个类型意味着别名，然后不论你对ref做什么。同样的动作都会对a也做。

Is that right? But why should one need aliasing?
对吗？那为什么要使用别名呢？

答案：
You are correct. A reference is similar to a const pointer in many regards (e.g. modifying the value of the reference modifies the original) and you cannot bind the reference to anything else but its initial variable,
你说的很正确，一个引用从很多方面看和一个const 指针是很相似的（比如，对引用的修改都会对被引用对象也修改），并且你不能把对初始变量的绑定重新绑定到其他变量上。

but it cannot be null and the syntax of using it is also different (no need for dereferencing (*, -&gt;)).
但是，引用不能为null，并且使用起来语法也有一点点不同（他不需要使用*和-&gt;）

Because it is not a pointer, the best description is alias: think of it as a different name for the same variable.
因为他不是一个指针，最好的一个描述就是别名：认为他是一一个变量的另一个名字

Also, if you pass a reference as a function parameter, no copy of the variable is made (like with a pointer and it pointed-to value), and any change to the reference reflects on the original variable.
而且，如果你在函数参数中传递一个引用，不会产生这个变量的一个副本（就像一个指针，但是他指向一个值），同时，任何对引用的改变都会作用到原始变量上。

There are also special rules for const references that allow a temporary value to live beyond its regular lifetime until the const reference it is bound to goes out of scope.
对于const 引用有很多不同的规则，比如，允许一个临时值存在甚至超过了他正常的生命周期，知道对他的const引用离开正常区域。才会消失。

原文：[http://stackoverflow.com/q/10667086/764869](http://stackoverflow.com/q/10667086/764869)