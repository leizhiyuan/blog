---
title: C＃中的is和as
tags:
  - C＃
  - 大学
  - 总结
  - 笔记
  - 软件
id: 1083
categories:
  - 学习笔记
date: 2012-07-01 05:52:09
---

is和as都是用作类型转换的，类型转换包括显示转换和隐式转换，在.NET中类型转换的基本规则如下：

### 基本规则

> *   任何类型都可以安全的转换为其基类类型，可以由隐式转换来完成；
> *   任何类型转换为其派生类型时，必须进行显示转换，转换的规则是：（类型名）对象名；
> *   使用GetType可以取得任何对象的精确类型；
> *   基本类型可以使用Covert类实现类型转换；
> *   除了string以外的其他类型都有Parse方法，用于将字符串类型转换为对应的基本类型；
> *   值类型和引用类型的转换机制称为装箱（boxing）和拆箱（unboxing）。
is和as操作符，是C#中用于类型转换的，提供了对类型兼容性的判断，从而使得类型转换控制在安全的范畴，提供了灵活的类型转换控制。

### is的模式如下：

> *   检查对象类型的兼容性，并返回结果，true或者false；
> *   不会抛出异常；
> *   如果对象为null，则返回值永远为false。
使用很简单，用于条件判断中.

举例：
<pre class="lang:c# decode:true">object o=new object();
if(o is ISSsample)
 {
    //转换
    ISSample a =(ISSample)o;
 }</pre>
&nbsp;

### as的模式如下：

> *   检查对象类型的兼容性，并返回转换结果，如果不兼容就返回null；
> *   不会抛出异常；
> *   如果结果判断为空，则强制执行类型转换将抛出NullReferenceException异常。
> *   as必须和引用类型一起使用

举例：
<pre class="lang:c# decode:true">object o =new object();
ASSample a=o as ASSample;
if(a!=null)
 //do some work</pre>
&nbsp;

参考：[http://www.cnblogs.com/anytao/archive/2007/04/07/must_net_01.html](http://www.cnblogs.com/anytao/archive/2007/04/07/must_net_01.html)