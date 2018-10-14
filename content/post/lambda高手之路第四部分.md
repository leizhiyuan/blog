---
title: Lambda高手之路第四部分
tags:
  - C＃
  - windows
  - 学习
  - 总结
  - 翻译
id: 2477
categories:
  - 我的翻译
date: 2012-12-24 19:20:12
---

首先祝大家平安夜快乐。本篇介绍一些流行的JavaScript模式。为下一篇打基础

使用/了解JavaScript的一个好处就是函数的高级用法。。在JavaScript里。函数仅仅是对象。他们可以有赋给他们的属性。而在C#中。我们不能做我们可以在JavaScript的全部事情。但是我们仍然可以做些事情。一个原因是JavaScript在函数里给变量以作用域。因此，不得不通过创建函数，大多数情况是匿名的来定位变量。而在C#中。通过使用块，通过花括号来创建作用域

当然，换种方式来说。C#中，函数也会给变量作用域。通过使用Lambda表达式。我们通过花括号在其里面创建了一个变量。然而。我们也可以局部的创建作用域。

我们来看看通过使用Lambda表达式可以实现一些在JavaScript里面有用的模式把。

**回调模式**

这个模式是个老的模式。事实上。回调模式从.net 的第一版就开始使用了。但是是以一种很简单的方式实现的。而现在。通过使用Lambda表达式。闭包，捕获变量等特性能够允许我们写出如下的代码来。

<pre class="lang:default decode:true " >void CreateTextBox()
{
	var tb = new TextBox();
	tb.IsReadOnly = true;
	tb.Text = "Please wait ...";
	DoSomeStuff(() =&gt; {
		tb.Text = string.Empty;
		tb.IsReadOnly = false;
	});
}

void DoSomeStuff(Action callback)
{
	// Do some stuff - asynchronous would be helpful ...
	callback();
}</pre> 

对于JavaScript程序员会觉得这没什么啊。他们使用这个模式太多了。然而，它非常有用。因为我们可以使用参数作为Ajax相关事件的事件处理器（比如oncompleted，onsuccess），等等。如果你使用LINQ，那么你可能也会用到回调模式的一些东西。举个例子。LINQ的where子句将会在每一次迭代中回调你的查询语句。这只是回调函数的一个例子。在.net的世界里。事件如它名字所暗示的那样。通常是事件处理的首选方法。这有时候很像一个回调。他有两个参数。有一个特殊的关键字和一个类型模式（两个参数分别是sender和arguments，sender通常是object类型。Arguments通常继承自EventArgs）
可以通过+= 和-=给事件添加/删除事件处理程序。

**返回方法**
和普通的方法比较。Lambda表达式也可以返回一个方法指针（就是一个委托实例），这意味着我们可以使用Lambda表达式创建/返回一个lambda表达式（或者今年仅是一个已定义好的方法的委托实例），大量的情况下。这个模式也很有用。首先看一下例子。

<pre class="lang:default decode:true " >Func&lt;string, string&gt; SayMyName(string language)
{
	switch(language.ToLower())
	{
		case "fr":
			return name =&gt; {
				return "Je m'appelle " + name + ".";
			};
		case "de":
			return name =&gt; {
				return "Mein Name ist " + name + ".";
			};
		default:
			return name =&gt; {
				return "My name is " + name + ".";
			};
	}
}

void Main()
{
	var lang = "de";
	//Get language - e.g. by current OS settings
	var smn = SayMyName(lang);
	var name = Console.ReadLine();
	var sentence = smn(name);
	Console.WriteLine(sentence);
}</pre> 

代码本应该更短些。我们可以让default如果请求的语言没有找到。只是抛出一个异常即可。不过。这个例子展示了这是一种方法工厂。另一种同等效果的方法是包含一个Hashtable。或者更好的话用Dictionary<K, V>

<pre class="lang:default decode:true " >static class Translations
{
	static readonly Dictionary&lt;string, Func&lt;string, string&gt;&gt; smnFunctions = new Dictionary&lt;string, Func&lt;string, string&gt;&gt;();

	static Translations()
	{
		smnFunctions.Add("fr", name =&gt; "Je m'appelle " + name + ".");
		smnFunctions.Add("de", name =&gt; "Mein Name ist " + name + ".");
		smnFunctions.Add("en", name =&gt; "My name is " + name + ".");
	}

	public static Func&lt;string, string&gt; GetSayMyName(string language)
	{
		//检查请求的语言是否可用
		return smnFunctions[language];
	}
}

//通过调用Translations.GetSayMyName("de") 获取德语方法</pre> 

即使这看起来似乎有点过度工程化了。但是也许这是方法工厂最好的例子了。毕竟方法简单易扩展，可以被用在很多情况下。这中模式和反射的组合可以是的程序代码更松耦合，更易使用。并且代码更健壮。下图展示了模式的工作流程
[![](/images/e6a03db9751c4ebd13b63fd2880f2267e3af2b44.png)](http://leaverimage.b0.upaiyun.com/30815_o.png)

**自定义方法**
自定义方法模式一个JavaScript中普遍的技术。可以用在代码中提高性能和健壮性，这种模式的背后思想是方法被设置为一个属性。于是可以和其他方法非常方便的交换。我们看一下具体的代码

<pre class="lang:default decode:true " >class SomeClass
{
	public Func&lt;int&gt; NextPrime
	{
		get;
		private set;
	}

	int prime;

	public SomeClass()
	{
		NextPrime = () =&gt; {
			prime = 2;

			NextPrime = () =&gt; {
				//获得下一个素数
				//设置素数
				return prime;
			};

			return prime;
		};
	}
}
</pre> 

这段代码做了什么？好吧。第一种情况下，我们得到第一个素数。也就是2.虽然这很显而易见。我们可以体征我们的算法默认来排除掉所有的偶数。这就加速我们的算法。但是我们依然能够得到开始的偶素数2.我们就可以通过调用NextPrime方法来获得素数了。我们就可以在第二部分里有话我们的算了。

我们已经看到这种写法可以优化性能。我们可能一下这个例子

<pre class="lang:default decode:true " >Action&lt;int&gt; loopBody = i =&gt; {
	if(i == 1000)
		loopBody = /* 1000之后的数进行的操作*/;

	/* 前1000个数进行的操作 */
};

for(int j = 0; j &lt; 10000000; j++)
	loopBody(j);

</pre> 

这里我们有了两根分开的区域。一个是为钱1000个数准备的。其他的则留在了另一个区域。通常我们可能需要区分这两种情况。这就是我们为什么使用了自定义方法，当小区域执行完后，自定义函数就会改变了。来执行大的区域了。

**即时执行方法表达式**

在JavaScript中即时执行方法表达式也叫做IIFEs,也相当普遍。这是因为在JavaScript中，不像C#中的花括号可以构造一个局部变量。因此一个变狼可能会污染一个全局对象。（最有可能就是一个window对象），由于很多原因，这种情况很不好。。

解决方法也很简单，当花括号不能给一个作用域的时候，方法却可以。因此，定义在方法里面的变量就被限制在这个方法里。就像是他的孩子一样。因此，通常JavaScript用户想让那些方法立刻执行。否则，是对变量名和语句行的浪费。另一个原因就是这个方法只执行一次。

在C#中我们也可以写出类似的方法。我们也会得到一个新的作用域。但这不是我们主要关心的。因为我们可以随心所欲创建新的作用域，我们看些例子

<pre class="lang:default decode:true " >(() =&gt; {
	// Do Something here!
})();</pre> 

这段代码很容易理解。然后，如果我们想要传一些参数时候。我们需要定义他们的类型你个，我们看一下如何给即时执行方法传递参数

<pre class="lang:default decode:true " >((string s, int no) =&gt; {
	// Do Something here!
})("Example", 8);</pre> 

看上去似乎没做什么。然而。我们可以和async关键字组合起来。我们看

<pre class="lang:default decode:true " >await (async (string s, int no) =&gt; {
	// 使用Tasks异步执行!
})("Example", 8);

//task完成后继续执行</pre> 

Ok了。

**即时对象初始化**

我把这个模式放在关于Lambda表达式的文章里是因为匿名对象比我们刚刚的一些简单的例子能表达的要多得多。其中一个就是匿名对象也可以包含Lambda表达式。这可以作为我写在本文里的理由。

<pre class="lang:default decode:true " >//创建匿名对象
var person = new {
	Name = "Florian",
	Age = 28,
	Ask = (string question) =&gt; {
		Console.WriteLine("The answer to `" + question + "` is certainly 42!");
	}
};

//执行方法
person.Ask("Why are you doing this?");</pre> 

如果你想要运行这个模式，你可能会看到一个异常。。至少我看到了。。这难以理解的理由是Lambda表达式不能被赋给匿名对象。如果你也没明白。。那我们看来是坐在一条船上的。
幸运的是，编译器已经告诉我们了。“哥们，我不知道我该为你的那个Lambda表达式创建哪种类型的委托啊”我们可以帮一下编译器。如下所示。

<pre class="lang:default decode:true " >var person = new {
	Name = "Florian",
	Age = 28,
	Ask = (Action&lt;string&gt;)((string question) =&gt; {
		Console.WriteLine("The answer to `" + question + "` is certainly 42!");
	})
};</pre> 

一个问题就产生了。方法（这里是Ask）存活的作用域是什么？答案是他存活在创建匿名对象的类里面，或者如果他使用了捕获变量，就在他自己的作用域里面。因此，编译器仍然创建了一个匿名对象（包含对编译器生成的类的元数据，实例化了一个有着类信息的新的对象），但是只是设置了委托对象Ask属性

注意：你应该避免使用这个模式，当你真的想要访问一个匿名对象的所有属性的时候。理由如下：C#编译器请求在你使用每一个对象之前，应该首先声明他。因此，用的地方一定在声明之后，但是编译器怎么知道？在编译器看来。Person变量还没声明完呢。就开始使用了。
所以，在上面那个Lambda表达式里并不能直接访问person的Name和Age属性
有一个方法跳出这个陷阱（事实上有很多种，但我只喜欢这一种最优雅的，考虑下面这段代码

<pre class="lang:default decode:true " >dynamic person = null;
person = new {
	Name = "Florian",
	Age = 28,
	Ask = (Action&lt;string&gt;)((string question) =&gt; {
		Console.WriteLine("The answer to `" + question + "` is certainly 42! My age is " + person.Age + ".");
	})
};

//执行方法。
person.Ask("Why are you doing this?");</pre> 

现在因为我们之前已经声明了，我们也可以说明person为object对象。但是这样的话我们就要用到反射机制了（可能还需要一些漂亮的包装器），来访问匿名对象的属性，在这种情况下，我们依赖于DLR，他会对这种情况出现一个最漂亮的包装器，现在这段代码就非常JavaScript化了，我也不知道是好还是坏，这也就是为什么我说这个是一个注意点。。

**初始时间分支**

这个模式事实上和自定义方法非常接近，唯一的不同，这种情况下，该方法并不定义自己，而是包含其他方法。显然是可能的。如果这个“其他方法”不是通过传统的方式定义，而是通过属性，（成员变量）

这种模式的别名也叫做加载时分支，是一个优化模式，这个模式用来避免switch-case或if-else语句等控制结构的大量使用。也可以理解为这个模式为代码永久性的创建了确定分支之间的连接。
考虑下面这个例子

<pre class="lang:default decode:true " >public Action AutoSave { get; private set; }

public void ReadSettings(Settings settings)
{
	/* 读取一些用户设置信息 */

	if(settings.EnableAutoSave)
		AutoSave = () =&gt; { /* 进行自动设置 */ };
	else
		AutoSave = () =&gt; { }; //什么都不做!
}</pre> 

我们都做了什么呢？首先我们有一个方法读取用户配置（由一个配置Setting类来保存），如果我们发现启用了自动设置。我们就给AutoSave这个属性赋上全部的代码，否则我们就放一个空方法在这里，而们就可以通过调用AutoSave（）一次就行了，不再需要检查配置设置或其他的什么情况了。我们也不需要保存一个特殊的布尔变量，因为对应的方法已经被动态的设置了。

有人也许认为这不是一个巨大的性能提高，但是这仅仅是一个小例子，在一些复杂的代码里，这会节省很多时间，尤其是代码越来越复杂的情况下。

另外（我认为这是最主要的原因），代码更加容易维护（如果你了解这个模式的话）没有了不必要的控制逻辑，程序员可以更加专注于重要的东西—调用这个自动保存的具体操作

在JavaScript中，像初始时间分支模式被用在很多性能检测上（比如浏览器检测）不去管浏览器检测实际上是很邪恶的，并且不应该在任何网页上检测用户的浏览器，
性能检测非常有用，通常和这种模式一起组合使用，这也是Jquery用来对Ajax请求检测需要的正确对象的方法。

下一部分介绍Lambda表达式超级有用的情况，同样圣诞节快乐。