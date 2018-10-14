---
title: "C#中的throw"
tags:
  - C＃
  - 大学
  - 学习
  - 翻译
id: 2295
categories:
  - 我的翻译
date: 2012-11-18 12:33:49
---

Throw会抛出/传递异常,通过在catch块里使用throw语句.可以改变产生的异常,比如我们可以抛出一个新的异常,throw语句有各种各样的,并且很有必要.

**例子**
我们首先看一下三个方法,分别叫做A,B,C，他们使用不同的throw语句。方法A使用了无参的throw语句。这可以被看作是rethrow(继续抛出)—他会抛出已经出现的同样的异常

继续，方法B throw一个命名的异常变量。这就不是一个完全的rethrow了—因为他虽然抛出了同样的异常。但是改变了StackTrace（堆栈轨迹），如果有必要的话，我们可以收集一些异常信息，而方法C则创建了一个新的异常。
提示:你可以通过这种方法实现自定义的的错误处理
使用throw语句的例子
<pre class="lang:default decode:true">using System;
class Program
{
    static void Main()
    {
	try
	{
	    A();
	    B();
	    C(null);
	}
	catch (Exception ex)
	{
	    Console.WriteLine(ex);
	}
    }

    static void A()
    {
	// Rethrow 语法.
	try
	{
	    int value = 1 / int.Parse("0");
	}
	catch
	{
	    throw;
	}
    }

    static void B()
    {
	// 过滤异常类型.
	try
	{
	    int value = 1 / int.Parse("0");
	}
	catch (DivideByZeroException ex)
	{
	    throw ex;
	}
    }

    static void C(string value)
    {
	// 创建新的异常.
	if (value == null)
	{
	    throw new ArgumentNullException("value");
	}
    }
}</pre>
程序可能的输出结果
<pre class="lang:default decode:true">System.DivideByZeroException: Attempted to divide by zero.
System.DivideByZeroException: Attempted to divide by zero.
System.ArgumentNullException: Value cannot be null.
Parameter name: value</pre>
**Rethrow**

接着我们看更多的关于rethrows的细节。Rethrow必须是一个无参的throw语句。如果使用throw ex，那么TargetSie(TargetSite 从堆栈跟踪中获取抛出该异常的方法。如果堆栈跟踪为空引用，TargetSite 也返回空引用。-译者注)和StackTrace都被改变了。

在下面的程序里，X()方法使用了rethrow语句。Y()使用了throw ex语句。我们可以看看当rethrow语句使用的使用，引发异常的方法，也就是异常的TargetSite是在StringToNumber---一个int.Parse内部的方法。

但是：当throw ex用的时候。就像在Y()里面，这个异常的TargetSite被修改到了当前的Y()方法里。
测试rethrow的例子
<pre class="lang:default decode:true">using System;

class Program
{
    static void Main()
    {
	try
	{
	    X();
	}
	catch (Exception ex)
	{
	    Console.WriteLine(ex.TargetSite);
	}

	try
	{
	    Y();
	}
	catch (Exception ex)
	{
	    Console.WriteLine(ex.TargetSite);
	}
    }

    static void X()
    {
	try
	{
	    int.Parse("?");
	}
	catch (Exception)
	{
	    throw; // [Rethrow 构造]
	}
    }

    static void Y()
    {
	try
	{
	    int.Parse("?");
	}
	catch (Exception ex)
	{
	    throw ex; // [Throw 捕获的ex变量]
	}
    }
}</pre>
输出
<pre class="lang:default decode:true">Void StringToNumber(System.String, ...)
Void Y()</pre>
**总结：**
异常处理机制提供了可选的控制路径，它将异常逻辑和异常处理分割开来。并且可以通过throw来rethrow异常或是生成一个新的异常。
译自：http://www.dotnetperls.com/throw

本文由Bystander翻译，转载请注明[http://leaver.me](http://leaver.me)