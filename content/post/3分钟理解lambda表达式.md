---
title: 3分钟理解Lambda表达式
tags:
  - C＃
  - windows
  - 翻译
  - 设计
  - 软件
id: 2402
categories:
  - 我的翻译
date: 2012-12-08 19:26:51
---

**1.什么是Lambda表达式**

Lambda表达式是一个匿名方法，通常在LINQ中被用来创建委托

简单来说。它是一个没有声明，没有访问修饰符，没有返回值。甚至没有名字的方法。

&nbsp;

**2.为什么我们需要使用Lambda表达式？或者说为什么我们要写一个没有名字的函数？**

为了方便，这种快捷方式允许你在调用的地方直接编写代码，尤其是你想调用的代码只会在这个地方使用一次。并且方法体本身很短。节省了单独写方法中写声明等等的麻烦。。

好处

1.代码量减少。不必写方法的名称。返回值和访问修饰符

2.当阅读代码的时候。直接就可以看到被调用函数的代码，不用去别的地方。

Lambda表示应该短些。太复杂了。可读性就下降了

&nbsp;

**如果编写Lambda表达式**

Lambda基本的定义是：参数=&gt;执行代码

举个例子
<pre lang="cs">n = > n % 2 == 1</pre>
n是输入参数
n % 2 == 1 是函数体

你可以读作：给这个匿名方法传入一个参数n，如果n是奇数就返回true

&nbsp;

**使用该Lambda的例子**
<pre class="lang:default decode:true">List&lt;int&gt; numbers = new List&lt;int&gt;{11,37,52};
List&lt;int&gt; oddNumbers = numbers.where(n =&gt; n % 2 == 1).ToList();
//现在oddNumbers 里面就是11和37了</pre>
ok.基本的Lambda表达式就是这样了。