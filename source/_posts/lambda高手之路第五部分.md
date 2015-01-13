title: Lambda高手之路第五部分
tags:
  - C＃
  - windows
  - 翻译
  - 软件
id: 2484
categories:
  - 我的翻译
date: 2012-12-27 09:15:29
---

武汉下雪了。。今天介绍Lambda表达式非常有用的情况。。ps:这个高手之路已经翻译了10000多字了。。疼啊。。

一些模式比另一些模式有时候更加合适，真正有用的模式是自定义方法表达式，用爱促使话一些对象的部分，我们考虑下面这种情况。

我们想要创建一个可以处理多种延迟加载的对象，这意味着即时对象已经被实例化了，我们还没有加载所有请求的资源，一个理由就是防止大量的IO操作。（比如通过网络传输），当我们开始使用数据的时候，我们想要确定数据足够新鲜，现在有一些确定的方法可以做这个。并且最有效的显然是实体框架已经用LINQ解决了延迟加载的问题，Iqueryable<T>仅存储了查询，而没有任何无关的数据。一旦我们请求一个结果。不仅仅构造的查询被执行，同时也被以更高效的方式执行，比如一个在远程数据服务器上的SQL查询。

在这里，我们仅仅想要看看两种情况的不同点，首先，我们查询，一切就绪，查询应该在已经加载了的数据上进行。

<pre class="lang:default decode:true " >class LazyLoad
{
	public LazyLoad()
	{
		Search = query =&gt; {
			var source = Database.SearchQuery(query);

			Search = subquery =&gt; {
				var filtered = source.Filter(subquery);

				foreach(var result in filtered)
					yield return result;
			};

			foreach(var result in source)
				yield return result;
		};
	}

	public Func&lt;string, IEnumerable&lt;ResultObject&gt;&gt; Search { get; private set; }
}
</pre> 

简单来看，这里我们有两种不他哦你的方法，地一个是我们把数据从数据库里提取出来（也就是Database静态类所做的），然后第二个方法将会过滤从数据库里提取出来的数据。一旦我们将会从我们的第一次查询取得结果，当然我们也可以构造内置的其他方法来重置类的行为，对于工业级的代码，其他的方法也许更加有用。

另一个例子是初始时间分支，假设我们有一个对象，该对象有个方法叫做Perform(),这个方法可以用来调用一些代码，包含这个方法的对象可以被初始化，初始化有三种方式。
1.	通过传递方法来调用
2.	通过传递一些包含这个方法的对象来调用
3.	或者通过传递第一种情况下的序列化以后的信息来调用。

现在我们可以保留所有的三种方式做全局变量。而Perform方法将不得不查看当前的状态（或者是保存在枚举变量里，或者和null进行比较）然后检测被调用的正确的方式，最后调用开始。

更好的一种方法是吧Perform()方法写成一个属性，这个属性仅仅允许在类里面进行set，它是一个委托类型，现在我们可以在对应的构造方法里面直接设置这个属性，因此，我们可以不用全局变量，也不用担心这个对象是如何实例化的，这种方法更好。

看一小段简单的代码。

<pre class="lang:default decode:true " >class Example
{
	public Action&lt;object&gt; Perform { get; private set; }

	public Example(Action&lt;object&gt; methodToBeInvoked)
	{
		Perform = methodToBeInvoked;
	}

	//接口
	public Example(IHaveThatFunction mother)
	{
		//传递的对象必须有我们要用的方法
		Perform = mother.TheCorrespondingFunction;
	}

	public Example(string methodSource)
	{
		//Compile方法是任意的。
		Perform = Compile(methodSource);
	}
}
</pre> 

即时这个例子看起来如我们所愿被构造了。让阿尔。大多数情况下只使用前两种，但是随着领域特性语言，编译器，日志框架，数据访问层和其他很多情况下，通常有很多方式可以完成，但Lambda表达式也许是最优雅的。

考虑这种情况，我们可以在函数编程领域体会到即时调用方法表达式的好处，我们可以看到C#中IIFE的一种用法。用的不多。但是我认为真的很好。但不是用在这种情况下。

<pre class="lang:default decode:true " >Func&lt;double, double&gt; myfunc;
var firstValue = (myfunc = (x) =&gt; {
	return 2.0 * x * x - 0.5 * x;
})(1);
var secondValue = myfunc(2);
//...</pre> 

我们也可以使用即时调用方法来防止一些确定的非静态的方法被重复调用。这就会出现自定义方法和初始时间分支和IIFE的组合使用了。

下一节介绍一些新的Lambda设计模式