---
title: Lambda高手之路第一部分
tags:
  - C＃
  - windows
  - 大学
  - 学习
  - 总结
  - 翻译
id: 2443
categories:
  - 学习笔记
date: 2012-12-18 19:38:42
---

好长时间没发技术文章了，恰好看到一篇非常详细的Lambda文章。一边翻译一边学习。题目好像有点霸气。。

# <span style="color: #0000ff;">**介绍**</span>

Lambda表达式是使代码更加动态，易于扩展并且更加快速（看完本文你就知道原因了）的强有力的工具。也可以用来降低潜在的错误。同时可以利用静态输入和智能提示，就像VS里一样。

&nbsp;

Lambda表达式在.net framework 3.5中提出来。并且在LINQ和ASP.NET MVC内部的一些技术中扮演了相当重要的角色。如果你考虑一下ASP.NET MVC中各类控件的实现。你就发现。奥妙就是他们大多使用了Lambda表达式。和Lambda表达式一起，使用Html扩展方法将会使得在后台创建模型成为可能。

本文会讲到如下的知识。

1.简短的介绍-Lambda表达式是什么，以及为什么和匿名方法不同（之前我们使用的）
2.走近Lambda表达式的性能-在哪些情况下比起标准方法，Lambda会提高/损失性能
3.深入-Lambda表达式在MSIL代码中是什么样
4.一些来自JS世界的模式映射到C#中
5.那些能够提高性能，并且代码看起来相当舒服的使用Lambda的情况。
6.一些我提出的新模式-当然有可能别人也提出来了。但这是我的思考结果。

&nbsp;

如果你期望本文是一篇入门教程我可能要让你失望了，除非你真的很优秀并且很聪明，当然我不是这种人，所以我也想提前声明一下：为了读懂这篇文章你可能需要C#的一些高级知识，并且对C#比较了解。

&nbsp;

你应该期望本文试着解释一些事情给你，也会解释一些有趣的问题，至少对我来说是这样的。最后我会展示一些实际的例子和模式，如我所说，Lambda表达式简化了很多情况。因此写显式的模式很有用。

# <span style="color: #0000ff;">**背景知识-什么是Lambda表达式**</span>

在C#1.0中，委托被提出了，它使得传递函数成为可能，一句话就是委托就是强类型的函数指针，但委托比指针更强大。一般传递一个函数需要如下几步。
1\. 写一个委托（就像一个类）包含返回类型和参数类型
2\. 使用委托作为某一个函数的参数类型，这样，该函数就可以接受和委托描述的有着相同签名的函数了
3\. 将一个委托类型的函数传递给委托，创建一个委托实例。

&nbsp;

如果听起来很复杂，确实本来很复杂，但这是必需的。（虽然不是造火箭，但是比你认为的要更多的代码），然而步骤三不是必需的，编译器会为你做他，但是步骤1和2却是必不可少的。

&nbsp;

幸运的是C#2.0出现了泛型，现在我们也可以写泛型类，方法，更重要的是，泛型委托，然而，直到.net framework 3.5的时候。微软意识到实际上只有两种泛型委托（当然有一些不同的重载），会覆盖99%的使用情况：

1.Action 没有任何输入参数，也没有输出参数。
2.Action&lt;t1,…t16&gt; 需要1-16个参数，没有输出参数。
3.Func&lt;t1….t16,tout&gt;需要0-16个参数，一个输出参数

&nbsp;

Action和其对应的泛型版本（仅仅是一个动作，执行一些事情）返回void的时候。Func则可以返回最后一个参数指定的类型，通过这两个委托类型，我们事实上，大部分情况下。前面提到的三步中的第一部就不用写的。而第二步仍然需要。

&nbsp;

那么如果我们想要运行代码的时候怎么做呢。在C#2.0中问题已经可以解决了。在这个版本里。我们可以创建委托方法，也就是一个匿名方法，然后这个语法一直未能流行起来，一个相当简化的匿名方法的版本类似这样：
<pre class="lang:default decode:true">Func&lt;double, double&gt;  square = delegate (double x) {
return x * x;
}</pre>
为了提高这种语法，欢迎来到Lambda表达式的国度。首先，这个Lambda名字怎么来的？事实上。来自于数学上的λ演算，更准确的说他是数学中一个正式的系统。用于通过变量绑定和替换来进行表达式计算，所以我们有0-N个输入参数和一个返回值，而在编程中，也可以没有返回值

# <span style="color: #0000ff;">我们看一下Lambda表达式的一些例子</span>

<pre class="lang:default decode:true">//编译器可以识别，然后就可以通过dummyLambda();来调用了
var dummyLambda = () =&gt; { Console.WriteLine("Hallo World from a Lambda expression!"); };

//可以通过类似 double y = square(25);来使用
Func&lt;double, double&gt; square = x =&gt; x * x;

//可以通过类似double z = product(9, 5);来使用
Func&lt;double, double,double&gt; product = (x, y) =&gt; x * y;

//可以通过类似printProduct(9, 5);来使用
Action&lt;double, double&gt; printProduct = (x, y) =&gt; { Console.Writeline(x * y); };

//可以通过类似var sum = dotProduct(new double[] { 1, 2, 3 }, new double[] { 4, 5, 6 });
Func&lt;double[], double[], double&gt; dotProduct = (x, y) =&gt; {
var dim = Math.Min(x.Length, y.Length);
var sum = 0.0;
for(var i = 0; i != dim; i++)
sum += x[i] + y[i];
return sum;
};

//可以通过类似 var result = matrixVectorProductAsync(...);使用
Func&lt;double[,], double[], double[]=""&gt; matrixVectorProductAsync = async (x, y) =&gt; {
var sum = 0.0;
/* do some stuff ... */
return sum;
};</pre>
从上面的代码段里我们可以学到一些东西

*   如果我们只有一个输入参数，我们可以省略()
*   如果我们有一个输入参数，并且向返回一个，那么我们可以省略{}和return关键字
*   我们可以让我们的Lambda表达式异步执行，只要加上async关键字就可以了
*   Var关键字一般都不可以用，只有在极少的情况下有才可以。
当然我们可以使用像平常那样使用var，如果我们指定了参数类型，这是可选的。因为类型可以从委托的类型中推断出来。而下面的情况都是错误的。请看下面的例子
<pre class="lang:default decode:true">var square = (double x) =&gt; x * x;

var stringLengthSquare = (string s) =&gt; s.Length * s.Length;

var squareAndOutput = (decimal x, string s) =&gt; {
var sqz = x * x;
Console.WriteLine("Information by {0}: the square of {1} is {2}.", s, x, sqz);
};</pre>
&nbsp;

大部分基本情况我们都知道。但是有些相当cool的东西（这使得他们在很多情况下很有用），我们考虑下面的代码片段
<pre class="lang:default decode:true">var a = 5;
var multiplyWith = x =&gt; x * a;
var result1 = multiplyWith(10); //50
a = 10;
var result2 = multiplyWith(10); //100</pre>
&nbsp;

这是可以的。因此你可以使用其他的变量。这比你想象的要更特殊。因为这里出现了捕获变量。这使得出现了一个闭包，考虑下面的情况。
<pre class="lang:default decode:true">void DoSomeStuff()
{
var coeff = 10;
var compute = (int x) =&gt; coeff * x;
var modifier = () =&gt; {
coeff = 5;
};

var result1 = DoMoreStuff(compute);

ModifyStuff(modifier);

var result2 = DoMoreStuff(compute);
}

int DoMoreStuff(Action computer)
{
return computer(5);
}

void ModifyStuff(Action modifier)
{
modifier();
}</pre>
&nbsp;

猜猜看这段代码会发生什么。首先我们创建了一个局部变量。和两个lambda表达式，第一额Lambda表达式展示在其他局部区域里访问局部变量是可以的。这已经很让人难以置信了。这意味着我们想要保护一个变量，但事实上他仍然可以在其他方法里被访问。而不论方法是被定义在内部还是其他类的里。

&nbsp;

而第二个Lambda表达式则模拟了一个Lambda表达式可以修改外部域变量。这意味着我们可以在其他方法里修改我们的局部变量，仅仅需要传一个在对应域里创建好的Lambda表达式就可以了。因此，我认为闭包有点魔幻的色彩了。就像并行编程一样。可能导致未期望的结果。就像在并行编程中的竞争情况。

为了展示一下未期望的结果。欢迎期待下一篇。。