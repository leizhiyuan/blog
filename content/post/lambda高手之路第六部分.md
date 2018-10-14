---
title: Lambda高手之路第六部分
tags:
  - C＃
  - windows
  - 翻译
  - 设计
id: 2489
categories:
  - 我的翻译
date: 2012-12-28 14:24:35
---

今天武汉地铁通车了，今天介绍一些新的Lambda设计模式，应该是最后一部分了。

本节介绍一些核心有lambda表达式的模式，我不认为他们完全是新的模式，但是至少我还没有看到有人给他们起过名字，我于是决定尝试取个可能好，也可能不好的名字，这样我起码能很容易的给别人描述，有话在先，许多模式相当强大，但是可能会引入潜在的bug，所以小心为上

**复杂的多态**

Lambda表达式也可以被用来创建一些多态（override），而不用使用abstract或者virtual关键字（当然这并不意味着就不能用），考虑如下的代码片段

<pre class="lang:default decode:true " >class MyBaseClass
{
	public Action SomeAction { get; protected set; }

	public MyBaseClass()
	{
		SomeAction = () =&gt; {
			//Do something!
		};
	}
}</pre> 

看起来没什么新的知识，我们创建一个类，里面有一个属性（一个lambda表达式），又一次JavaScript化了，有趣的地方是：属性暴露的这个部分不只是本类可以改变，子类也可以改变，看代码

<pre class="lang:default decode:true " >class MyInheritedClass : MyBaseClass
{
	public MyInheritedClass
	{
		SomeAction = () =&gt; {
			//Do something different!
		};
	}
}
</pre> 

看到了。我们可以改变这个方法。或者进行更精确的操作，这种方法的缺点是我们不能直接访问父类的实现，也就缺乏了基类的能力，因为，这个父类的属性会有同样的值，如果程序员真的需要这样写，我建议你遵循 *pattern*

<pre class="lang:default decode:true " >class MyBaseClass
{
	public Action SomeAction { get; private set; }

	Stack&lt;Action&gt; previousActions;

	protected void AddSomeAction(Action newMethod)
	{
		previousActions.Push(SomeAction);
		SomeAction = newMethod;
	}

	protected void RemoveSomeAction()
	{
		if(previousActions.Count == 0)
			return;

		SomeAction = previousActions.Pop();
	}

	public MyBaseClass()
	{
		previousActions = new Stack&lt;Action&gt;();

		SomeAction = () =&gt; {
			//Do something!
		};
	}
}
</pre> 

这样的话，子类就不得不拥有了 AddSomeAction()  方法，而这个方法是吧当前的方法压入堆栈，那样我们可以恢复之前的状态。

这种模式我起了一个名字叫做Lambda属性多态模式（LP3），它简单的描述了可以在属性里捕获任何方法。之后可以被子类所设置，栈是这个模式的一个附加品，没有改变我们使用属性来完成的模式目标

为什么要用这种模式？有几个理由。地一个，因为我们可以用。但是等一等。如果你使用当中不同的属性，这个模式会变得相当棘手，突然，多态变成了一个完全的新方法。但是这也许是一个不同的模式，现在我想说这个模式完成了以前人们认为不可能的事情

举个例子，你想要（不建议，但是也许对该问题是最优雅的解决方法了。）重写一个静态方法，好吧。静态不可能被继承，原因很简单，师承是对实例对象来说的。而静态方法不属于任何一个实例，对于所有的实例都是一样的。这会引发一个警告，下面的例子也许并不如你所想的结果，因此，除非你非常清楚。否则不要乱用。
看代码

<pre class="lang:default decode:true " >void Main()
{
	var mother = HotDaughter.Activator().Message;
	//mother = "I am the mother"
	var create = new HotDaughter();
	var daughter = HotDaughter.Activator().Message;
	//daughter = "I am the daughter"
}

class CoolMother
{
	public static Func&lt;CoolMother&gt; Activator { get; protected set; }

	//此处是防止空引用
	static CoolMother()
	{
		Activator = () =&gt; new CoolMother();
	}

	public CoolMother()
	{
		//Message of every mother
		Message = "I am the mother";
	}

	public string Message { get; protected set; }
}

class HotDaughter : CoolMother
{
	public HotDaughter()
	{
		//一进入构造函数我们设置Activator ...
		Activator = () =&gt; new HotDaughter();
		//Message of every daughter
		Message = "I am the daughter";
	}
}
</pre> 

这很简单，希望没有对你产生误导，这种模式有时候会让事情变得异常复杂，这也是为什么我总是避免使用它。不过他很有用。（可以通过该方法构造所有的静态属性和方法，并且可以使你总是获得你感兴趣的那个）只要你不感到头疼，这是解决静态多态性的一个好方法。是的。静态多态性是可能的。

**简单请求一个方法**

之前我已经介绍过这个方法了。但是我没有说名字，这就是方法字典模式，这个模式的一个基础就是一个哈希表或是一个字典，包含一个keys（通常是字符串，但取决于具体的情况），这些keys对应一些特定的方法，这个模式也指定了一个特殊的方法构造这些字典，否则，一个简单的switch-case就搞定了，看代码

<pre class="lang:default decode:true " >public Action GetFinalizer(string input)
{
	switch
	{
		case "random":
			return () =&gt; { /* ... */ };
		case "dynamic":
			return () =&gt; { /* ... */ };
		default:
			return () =&gt; { /* ... */ };
	}
}
</pre> 

这哪里需要字典？好像不需要。事实上我们写的更好一些。

<pre class="lang:default decode:true " >Dictionary&lt;string, Action&gt; finalizers;

public void BuildFinalizers()
{
	finalizers = new Dictionary&lt;string, Action&gt;();
	finalizers.Add("random", () =&gt; { /* ... */ });
	finalizers.Add("dynamic", () =&gt; { /* ... */ });
} 

public Action GetFinalizer(string input)
{
	if(finalizers.ContainsKey(input))
		return finalizers[input];

	return () =&gt; { /* ... */ };
}
</pre> 

等等，这个模式现在没有优点了。。事实上，这个模式并不特别好写，她还需要多写写代码，但是我们可以使用反射来自动化这个字典的构造，这样我们就会和switch-case一样搞笑了。但是我们的代码更加健壮，并且维护更简单，如果你写switch-case。。你就需要手工添加很多分支语句了。。

我们看一种可能的实现方法。我通常更倾向于添加一些类型转换，这样就可以方便的使用keys对应的值了。当然，你也可以通过其他方法实现，比如使用一致的方法签名，这里我还是用转换。

<pre class="lang:default decode:true " >static Dictionary&lt;string, Action&gt; finalizers;

//此方法应有静态方法调用
public static void BuildFinalizers()
{
	finalizers = new Dictionary&lt;string, Action&gt;();

	//获得当前程序集的所有类型
	var types = Assembly.GetExecutingAssembly().GetTypes();

	foreach(var type in types)
	{
		//检查是否是一个确定类型
		if(type.IsSubclassOf(typeof(MyMotherClass)))
		{
			//获取构造函数
			var m = type.GetConstructor(Type.EmptyTypes);

			//如果默认构造函数就调用
			if(m != null)
			{
				var instance = m.Invoke(null) as MyMotherClass;
				//使用转换得到名称
				var name = type.Name.Remove("Mother");
				//名称可能不同，假定是MyMethod
				var method = instance.MyMethod;

				finalizers.Add(name, method);
			}
		}
	}
} 

public Action GetFinalizer(string input)
{
	if(finalizers.ContainsKey(input))
		return finalizers[input];

	return () =&gt; { /* ... */ };
}</pre> 

现在看起来好多了，事实上。这个模式节省了我很多时间。这个模式最好的就是：它使你可以写出优雅的插件。通过不同的库扩充功能。为什么。你可以使用该方法来扫描未知的库，这些库符合一些确定的模式。然后包含他们进入你的代码，这样。没有任何问题。其他库的方法就会继承成你的代码里。你所需要做的仅仅如下：

<pre class="lang:default decode:true " >//The start is the same

internal static void BuildInitialFinalizers()
{
	finalizers = new Dictionary&lt;string, Action&gt;();
	LoadPlugin(Assembly.GetExecutingAssembly());
}

public static void LoadPlugin(Assembly assembly)
{
	//This line has changed
	var types = assembly.GetTypes();

	//The rest is identical! Perfectly refactored and obtained a new useful method
	foreach(var type in types)
	{
		if(type.IsSubclassOf(typeof(MyMotherClass)))
		{
			var m = type.GetConstructor(Type.EmptyTypes);

			if(m != null)
			{
				var instance = m.Invoke(null) as MyMotherClass;
				var name = type.Name.Remove("Mother");
				var method = instance.MyMethod;
				finalizers.Add(name, method);
			}
		}
	}
} 

//The call is the same</pre> 

现在，我们所需要做的就是确定加载的插件，并且添加一些方法来处理这些事事情。最后。可以通过调用LoadPlugin()方法从给定的程序集实例上加载方法。这只是这个模式的一个应用。而我其实还可以做很多。。。比如我们也在JavaScript里面使用。那里可是没有内置的反射的。。