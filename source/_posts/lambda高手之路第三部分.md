title: Lambda高手之路第三部分
tags:
  - C＃
  - windows
  - 大学
  - 翻译
id: 2465
categories:
  - 我的翻译
date: 2012-12-20 20:12:54
---

背后的秘密-MSIL

通过著名的LINQPad，我们可以更深入的查看MSIL代码而没有任何秘密。下图是一个LINQPad的使用截图
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/2cc26ed754b0ac72d7caf372a917ef44d327d51a.png)](http://leaverimage.b0.upaiyun.com/30624_o.png)
=======
[![](/images/2cc26ed754b0ac72d7caf372a917ef44d327d51a.png)](http://leaverimage.b0.upaiyun.com/30624_o.png)
>>>>>>> 换电脑之后重新备份

我们会看三个例子，第一个Lambda表达式如下：

<pre class="lang:default decode:true " >Action&lt;string&gt; DoSomethingLambda = (s) =&gt;
{
	Console.WriteLine(s);// + local
};</pre> 

对应的普通函数是这样的

<pre class="lang:default decode:true " >Action&lt;string&gt; DoSomethingLambda = (s) =&gt;
{
	Console.WriteLine(s);// + local
};
</pre> 

生成的MSIL代码片段如下：

<pre class="lang:default decode:true " >DoSomethingNormal:
IL_0000:  nop         
IL_0001:  ldarg.1     
IL_0002:  call        System.Console.WriteLine
IL_0007:  nop         
IL_0008:  ret         
&lt;Main&gt;b__0:
IL_0000:  nop         
IL_0001:  ldarg.0     
IL_0002:  call        System.Console.WriteLine
IL_0007:  nop         
IL_0008:  ret       </pre> 

最大的不同是方法的名称用法不同。而不是声明。事实上。声明是完全一样的。编译器在类里面创建了一个新的方法来实现这个方法。这没什么新东西，仅仅是为了我们使用Lambda表达式方便代码编写。从MSIL代码中，我们做了同样的事情。在当前对象里调用了一个方法。

我们在下图里演示一下编译器所做的修改。在这个图例。我们可以看到编译器把Lambda表达式移动成了一个固定的方法。

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/1500b574e9e4fd0cfb11b286eaff270102810b02.png)](http://leaverimage.b0.upaiyun.com/30625_o.png)
=======
[![](/images/1500b574e9e4fd0cfb11b286eaff270102810b02.png)](http://leaverimage.b0.upaiyun.com/30625_o.png)
>>>>>>> 换电脑之后重新备份
第二个例子将展示Lambda表达式真正的奇妙之处，在这个例子里。我们既使用了有着全局变量的普通方法也使用了有捕获变量的Lambda表达式。代码如下

<pre class="lang:default decode:true " >void Main()
{
	int local = 5;

	Action&lt;string&gt; DoSomethingLambda = (s) =&gt; {
		Console.WriteLine(s + local);
	};

	global = local;

	DoSomethingLambda("Test 1");
	DoSomethingNormal("Test 2");
}

int global;

void DoSomethingNormal(string s)
{
	Console.WriteLine(s + global);
}
</pre> 

没什么不同的似乎。关键是：lambda表达式如何被编译器处理

<pre class="lang:default decode:true " >IL_0000:  newobj      UserQuery+&lt;&gt;c__DisplayClass1..ctor
IL_0005:  stloc.1     
IL_0006:  nop         
IL_0007:  ldloc.1     
IL_0008:  ldc.i4.5    
IL_0009:  stfld       UserQuery+&lt;&gt;c__DisplayClass1.local
IL_000E:  ldloc.1     
IL_000F:  ldftn       UserQuery+&lt;&gt;c__DisplayClass1.&lt;Main&gt;b__0
IL_0015:  newobj      System.Action&lt;System.String&gt;..ctor
IL_001A:  stloc.0     
IL_001B:  ldarg.0     
IL_001C:  ldloc.1     
IL_001D:  ldfld       UserQuery+&lt;&gt;c__DisplayClass1.local
IL_0022:  stfld       UserQuery.global
IL_0027:  ldloc.0     
IL_0028:  ldstr       "Test 1"
IL_002D:  callvirt    System.Action&lt;System.String&gt;.Invoke
IL_0032:  nop         
IL_0033:  ldarg.0     
IL_0034:  ldstr       "Test 2"
IL_0039:  call        UserQuery.DoSomethingNormal
IL_003E:  nop         

DoSomethingNormal:
IL_0000:  nop         
IL_0001:  ldarg.1     
IL_0002:  ldarg.0     
IL_0003:  ldfld       UserQuery.global
IL_0008:  box         System.Int32
IL_000D:  call        System.String.Concat
IL_0012:  call        System.Console.WriteLine
IL_0017:  nop         
IL_0018:  ret         

&lt;&gt;c__DisplayClass1.&lt;Main&gt;b__0:
IL_0000:  nop         
IL_0001:  ldarg.1     
IL_0002:  ldarg.0     
IL_0003:  ldfld       UserQuery+&lt;&gt;c__DisplayClass1.local
IL_0008:  box         System.Int32
IL_000D:  call        System.String.Concat
IL_0012:  call        System.Console.WriteLine
IL_0017:  nop         
IL_0018:  ret         

&lt;&gt;c__DisplayClass1..ctor:
IL_0000:  ldarg.0     
IL_0001:  call        System.Object..ctor
IL_0006:  ret      
</pre> 

和第一个例子一样。机制相同。编译器把lambda表达式移动到一个方法里。但是不同的是，编译器这次还生成了一个类。编译器为我们的lambda表达式生成的方法会放在类里，这就给了捕获的变量一个全局的作用域，通过这样。Lambda表达式可以访问局部变量。因为在MSIL里。它是类实例里面的一个全局变量。

所有的变量因此就可以在新生成的类的对象里赋值/读取了。这解决了变量之间的引用问题。（其实就是只保留了对该类实例的引用。）编译器也足够智能之会把这些捕获的变量放到类里面。因此，当我们使用Lambda的时候才没有太大的性能问题。无论如何。注意。由于保持了对lambda表达式的引用，因此可能造成内存泄漏。只要方法还在。变量就仍然存活。显而易见。而现在我们知道了原因。

我们再次用图示来说明。这这种闭包情况下里。不仅仅方法会被移动。捕获的变量也会被移动。所有被移动了的对象会被放到一个新生成的类里。因此一个没有名称的类就隐式的出现了。
<<<<<<< HEAD
 [![]({{BASE_PATH}}/images/708c4bd6bea1fa2d592281cebf202af1b36b4e14.png)](http://leaverimage.b0.upaiyun.com/30626_o.png)
=======
 [![](/images/708c4bd6bea1fa2d592281cebf202af1b36b4e14.png)](http://leaverimage.b0.upaiyun.com/30626_o.png)
>>>>>>> 换电脑之后重新备份

下一节将是映射流行的JavaScrpit模式。