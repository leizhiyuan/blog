title: Lambda高手之路第二部分
tags:
  - C＃
  - windows
  - 大学
  - 翻译
id: 2459
categories:
  - 我的翻译
date: 2012-12-19 20:03:20
---

# <span style="color: #0000ff;">闭包的影响</span>

为了展示闭包的影响，我们看下面这个例子。
<pre class="lang:default decode:true">var buttons = new Button[10];

for(var i = 0; i &lt; buttons.Length; i++)
{
	var button = new Button();
	button.Text = (i + 1) + ". Button - Click for Index!";
	button.OnClick += (s, e) =&gt; { Messagebox.Show(i.ToString()); };
	buttons[i] = button;
}
//如果我们点击按钮会发生什么</pre>
这个问题很怪，我在我的JavaScript课程上经常问我的学生。95%的学生会说。显然按钮0显示0，按钮1显示1，等等。而不足5%的学生学习了闭包之后会明白。所有的按钮都会显示10.

局部变量i的值改变了。并且等于buttons.Length。也就是10了。想要避免这个诡异的情况也很简单。如下就行了。
<pre class="lang:default decode:true">var button = new Button();
var index = i;
button.Text = (i + 1) + ". Button - Click for Index!";
button.OnClick += (s, e) =&gt; { Messagebox.Show(index.ToString()); };
buttons[i] = button;</pre>
问题解决了，但是index变量是一个值类型，因此保留了“全局”i的一个拷贝

最后一个话题是一个叫做表达式树的东西，他和Lambda表达式协作。并且，他会使得在ASP.NET MVC中的Html扩展方法发生一些很奇妙的东西。关键的问题是：如何发现目标函数

1\. 传递进去的变量的名称是什么
2\. 方法的主体是什么
3\. 函数体里使用了什么类型

Expression 解决了这些问题，他允许我们挖掘生成的表达式树，我们也可以执行传递给Func和Action委托的函数，而且，可以在运行时解析Lambda表达式

我们看一下如何使用Expression 类型的例子
<pre class="lang:default decode:true">Expression&lt;Func&lt;MyModel, int&gt;&gt; expr = model =&gt; model.MyProperty;
var member = expr.Body as MemberExpression;
var propertyName = memberExpression.Member.Name; //当 member != null的时候执行  ...</pre>
这是Expression最简单的用法了。规则也相当直接。通过构建一个Expression类型的对象。编译器为生成的解释树生成一些元数据。这个解释树包含所有相关的信息，比如参数和方法体。

方法体包含完整的解释树，我们可以访问这些操作。就像完整的语句一样。也可以操作返回指和类型。当然，返回可以为null，无论如此。大多数情况下。我们对表达式很感兴趣。这和ASP.NET MVC中处理Expression类型的方法是很相似的—可以得到使用的参数的名字，而好处是显而易见的。不会出现名称拼写错误了。也就不会出现因此而出现的编译错误了。

注意：当程序员仅仅对调用的属性的名字感兴趣的时候。有一个更简单，更优雅的解决方案，那就是参数特性CallerMemberName 这个特性可以得到调用方法/属性的名称。而字段被编译器自动填充。因此，如果我们仅仅想知道名字，而不想知道其他更多的信息，那么我我们只需要写出像下面这个例子里的代码就行了。通过调用WhatsMyName() 方法可以返回方法的名字
<pre class="lang:default decode:true">string WhatsMyName([CallerMemberName] string callingName = null)
{
    return callingName;
}</pre>

# <span style="color: #0000ff;">Lambda表达式的性能</span>

有一个很大的问题：Lambda表达式有多快？好吧。首先，我们期望他们能和我们一般的函数一样快。下一节里。我们会看到Lambda的MSIL代码和普通的函数没有太大的不同。

最有趣的一个讨论是如果Lambda表达式产生了闭包，将会和使用全局变量的方法一样快。这就产生了一个有趣的问题，和一个区域内局部变量的数目多少会有关系吗？

我们看看测试代码，通过4个不同的指标，我们可以看到普通方法和Lambda方法的不同。
<pre class="lang:default decode:true">using System;
using System.Collections.Generic;
using System.Diagnostics;

namespace LambdaTests
{
	class StandardBenchmark : Benchmark
    {
		const int LENGTH = 100000;
        static double[] A;
		static double[] B;

        static void Init()
        {
            var r = new Random();
            A = new double[LENGTH];
            B = new double[LENGTH];

            for (var i = 0; i &lt; LENGTH; i++)
            {
                A[i] = r.NextDouble();
                B[i] = r.NextDouble();
            }
        }

        static long LambdaBenchmark()
        {
            Func&lt;double&gt; Perform = () =&gt;
            {
                var sum = 0.0;

                for (var i = 0; i &lt; LENGTH; i++)
                    sum += A[i] * B[i];

                return sum;
            };
            var iterations = new double[100];
            var timing = new Stopwatch();
            timing.Start();

            for (var j = 0; j &lt; iterations.Length; j++)
                iterations[j] = Perform();

            timing.Stop();
            Console.WriteLine("Time for Lambda-Benchmark: \t {0}ms", timing.ElapsedMilliseconds);
            return timing.ElapsedMilliseconds;
        }

        static long NormalBenchmark()
        {
            var iterations = new double[100];
            var timing = new Stopwatch();
            timing.Start();

            for (var j = 0; j &lt; iterations.Length; j++)
                iterations[j] = NormalPerform();

            timing.Stop();
            Console.WriteLine("Time for Normal-Benchmark: \t {0}ms", timing.ElapsedMilliseconds);
            return timing.ElapsedMilliseconds;
        }

        static double NormalPerform()
        {
            var sum = 0.0;

            for (var i = 0; i &lt; LENGTH; i++)
                sum += A[i] * B[i];

            return sum;
        }
    }
}</pre>
本来用Lambda表达式我们可以把代码写的更好。最终我没有这样做。是为了防止影响看最后的结果。因此，本质上。上述代码里有三个方法
1个是Lambda测试，一个是普通测试，还有一个是在普通测试里调用的方法。而没有的第四个方法其实是我们的lambda表达式。在第一个方法里已经创建了。计算过程很简单，我们随机取数字防止编译器做任何优化。最后我们对普通方法和Lambda方法的不同很有兴趣。

如果我们运行这个测试程序。我们会发现Lambda表达式并不总是比普通方法差。我们惊奇的是有时候甚至Lambda表达式更快一些。然而，在有闭包的情况下，，就不对了。这就是说大多数情况下我们可以毫不犹豫的使用lambda表达式。当使用闭包的时候会出现一点点的性能损失，不过还好，下一节会讲述性能损失的几个原因。
下面是测试输出的结果表

Test Lambda [ms] Normal [ms]
0 45+-1                       46+-1
1 44+-1                       46+-2
2 49+-3                      45+-2
3 48+-2                      45+-2

下图是上表的结果，我们可以看得到普通的方法和Lambda表达式基本上限差不多。当使用Lambda表达式的时候没有太大的性能损失。

[![]({{BASE_PATH}}/images/b4702d953670f850f72d24daf0aefd146eb33227.png)](http://leaverimage.b0.upaiyun.com/30616_o.png)

第三部分会介绍背后的秘密-MSIL