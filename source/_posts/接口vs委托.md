title: 接口VS 委托
tags:
  - C＃
  - windows
  - 大学
  - 学习
  - 翻译
id: 1898
categories:
  - 我的翻译
date: 2012-10-07 08:31:54
---

# 背景

对于指定的任务有不同的方案可供选择，通常是很好的。因为可能某一种方案会更加适合该任务，但是有时候做决定会很难。因为这些不同的方案有其各自的优缺点。

我经常会停下来好好想想，是不是接口比委托更适合或者是更不适合某个任务。有时候我甚至会回去看我写的代码，这些代码刚开始使用委托来实现，我后来用接口替换掉。因此，是时候写篇文章来阐述一下这两种技术的优缺点了。

&nbsp;

# 性能

我经常看到有人问接口是不是比委托更快啊。或者是不是相反。通常。别人给的答案会是：

1.  接口更快。委托相当慢
2.  委托更快，因为他们是指向方法的指针，接口则需要一个v-table(虚函数解析表)，然后找到委托
3.  他们一样快，但委托更容易使用
&nbsp;

好吧。那些都是错的。也许在.Net 1中。委托真的很慢。但是事实是：

1.  委托执行（execute）的时候更快
2.  接口获得（get）的时候更快
在下面这段代码中：

&nbsp;
<pre class="lang:default decode:true">Action action = SomeMethod;</pre>
我们将得到一个Action(委托类型)来调用SomeMethod。问题是：委托是包含被调用方法的实例和指针的引用类型。而不仅仅只是一个指向方法的指针，通过引用类型，委托需要分配内存，因此，每一次你把一个方法变换成一个委托的时候。都会分配一个新的对象。

如果委托是一个值类型。会有些不一样。但是他们不是。。

另一方面，如果我们这样写代码：
<pre class="lang:default decode:true">IRunnable runnable = this;</pre>
&nbsp;

如果实现了IRunnable接口的对象。我们简单通过一个转换得到同样的引用。没有涉及内存分配。我们将可以通过下面的代码来进行速度比较：

对于委托：
<pre class="lang:default decode:true">Stopwatch stopwatch = new Stopwatch();
stopwatch.Start();
for(int i=0; i&lt;COUNT; i++)
{
  Action action = SomeMethod;
  action();
}
stopwatch.Stop();
Console.WriteLine(stopwatch.Elapsed);</pre>
&nbsp;

对于接口
<pre class="lang:default decode:true">Stopwatch stopwatch = new Stopwatch();
stopwatch.Start();
for(int i=0; i&lt;COUNT; i++)
{
  IRunnable runnable = this;
  runnable.Run();
}
stopwatch.Stop();
Console.WriteLine(stopwatch.Elapsed);</pre>
我知道接口会更快。不是因为他们执行更快。而是因为每一次迭代，一个新的Action委托都会被分配。 但是。如果把委托和接口的获得语句放在循环之外。委托会更快一些。

当创建事件的时候。举个例子。我们在给事件添加委托的时候，就只添加一次。这样。即使事件被触发再多次。也只进行了一次内存分配。

## 那么？谁赢了？

好。对于事件，委托将会更快些。

但是。在说委托更好或是更快之前，我们再看另一种情况。

&nbsp;

# 匿名方法

在我看来，匿名方法是委托最糟糕的使用。但是同时。也正在变成最普遍的用法。

当你像这段代码这样调用的时候
<pre class="lang:default decode:true">for(int i=0; i&lt;count; i++)
  MethodThatReceivesADelegate(() =&gt; SomeCall(i));</pre>
事实上，编译器将会创建一个接受参数i的方法实例，然后创建另一个实例（即委托）来引用这个实例。

如果用接口来替换的话。编译器将指挥分配单一的对象，该对象实现了接口

&nbsp;

## 可能的抱怨

一些人也许对那个接受参数i的方法实例的问题有所疑惑。他们可能认为，在每一次迭代中。实例里面的之被改变了。也许编译器可以优化这个委托的内存分配。实际只分配了一次。

好把。对于委托的内存分配我不知道。但是。对于要分配一个接受参数i的单一实例，确实真的。也是一个bug。如果MethodThatReceivesADelegate 把委托传递给另一个线程。其他的线程也许会接收到错误的i值，在.net 4.5中。这块不会出错。因为。每一次迭代。一个新的对象被创建。这就能保证当委托被传递到另一个线程里的时候。结果总是正确的。但这也就意味着每次都会有一个新的委托会创建。

如果MethodThatReceivesADelegate 仅仅使用委托一次。使用接口也许更好。不幸的是。我们没有办法实现匿名接口。

好。如果是为了方便。委托更好。但是如果要求性能。在这种情况下。接口会更好。因为避免了一次不必要的内存分配。

事实上，我创建了IRunnable接口是为了强制使用者实现了一个新的类型，而不是使用匿名委托。这样就可以解决在for循环（或是任何在foreach里使用的任何值）i值可变的问题，同时也有一定的性能提升。。

&nbsp;

# 调用和动态调用

&nbsp;

现在我们知道有匿名委托，但是没有匿名接口，只使用一次的情况下，接口将会比委托有更好的性能。因为只请求一个对象而不是两个。

这让我开始考虑，当一个方法接受一个只会执行一次的方法作为参数的时候，我应该使用委托还是是用接口。

但是。更多的性能相关的情况下我们可以这样用。。

你是否曾经有过要用动态调用代替直接委托调用的情况？一般是在编译时并不知道委托的参数类型的情况下。

好。当有一个接口。在接口的方法里有一个方法调用的参数类型未定。我不知道为什么。但是反射调用和委托的动态调用都极慢。比直接对参数做转换都慢。而数组长度验证。然后放到一个try/catch块里会抛出TargetInvocationException 异常。

因此。如果你有类似下面的代码：
<div>
<pre class="lang:default decode:true">public interface IDynamicInvokable

{

  object DynamicInvoke(params object[] parameters);

}</pre>
</div>
那么你可以创建你的委托接口，是IDynamicInvokable 接口的继承接口，像这样：
<div>
<pre class="lang:default decode:true">public interface IAction&lt;T&gt;:

  IDynamicInvokable

{

  void Invoke(T parameter);

}</pre>
</div>
这样你的用户就可以通过Invoke方法调用你的接口，如果他们在编译时不知道接口的类型。他们可以使用更泛化一些的IDynamicInvoke。

注意：我讨厌泛型这个名字。对于我来说。IDynamicInvoke 是调用方法最泛型的的途径，而IAction&lt;T&gt; 是类型接口，因此，我我说泛型的时候。我其实是在说更加普遍无类型的调用泛型。而不是类型指定的泛型。

那么，如果对委托做上千次调用。但是使用DynamicInvoke 代替Invoke，接口会做的更好

我又一次的问我自己。匿名委托的简单性值得吗？仅仅为了更好的性能我就把让我的用户对我方法的调用变得困难？并且这真的会影响到程序的整体性能吗？

&nbsp;

## 泛型，差异，无类型的使用

&nbsp;

我刚刚说我讨厌泛型的名字。因为使用泛型的代码通常是有类型的代码，而我们也许需要的无具体类型的代码，我认为这样更加泛一些。

但是。让我好好讨论一下.net的泛型。假设你知道一个委托的参数数目，但是你不知道具体的类型，这和DynamicInvoke 是不一样的。因为这个方法。简单的把所有的参数当成一个数组。

泛型具化或者是相反可以有一些帮助。但是很小。

比如。我们可以把 `Func&lt;string&gt;` 当成 `Func&lt;object&gt; ``，或是把``Action&lt;object&gt;` 看成 `Action&lt;string&gt;`

`理由很简单，当返回一个值的时候（``Func``的情况），``string``是一个``object``。将不会做任何转换。将会简单地返回一个``string``，调用这会看成一个无类型的``object``。但是可以。而在``Action``这个情况下。它需要一个``object``，而``string`.是可用的object，所以也可以。

但是。如果你想要把`Func&lt;int&gt;` 当作Func&lt;object&gt;。更广泛一点。想把所有的参数转换成object，可以吗？

答案是否定的。即使int在.net中也是object。但是。所有的值类型需要装箱。这个一个额外的动作。想要简单的把int作为object，而不进行装箱操作。将会引发很多问题。这也就是他不被支持的原因。

但是如果设计的好，接口会有个好处。我个人的原则是：没当有一个泛型类型的时候（可能是类或是接口），我会创建更泛型的一个接口，好吧。一个无类型的接口。该接口有所有的方法和性，但是使用object代替泛型参数类型。他会作为泛型类型的基类接口。

也就是说。如果我有一个IAction&lt;T&gt;，我会创建一个IAction接口。如果有IAction&lt;T1, T2&gt;，我会创建IAction2接口。

事实是：我更愿意Action&lt;int&gt; 能够被看作Action&lt;&gt;，然后让。Net可以知道我想要使用一个无类型的泛型委托。但是.net 不支持对泛型类型的无类型的使用。所以。我添加一些额外，有着无类型方法和属性的接口，到我的泛型类和接口里。然后就可以了。但是。对于委托来说这是不可能的。因此，此处接口更好。

&nbsp;

# 不同的用法

我们已经讨论过Invoke（调用）和DynamicInvoke（动态调用）了，那么TryInvoke呢？

我的上两篇文章讨论了这种转换。我们下面会回到那种情况下。

如果我们使用`Converter&lt;TInput, TOutput&gt;`  委托。转换可能正常也可能抛出一个异常。如果代码可以处理不正确的值，那么抛出异常对于表示转换失败就不合适了

我考虑创建另一个委托（TryConverter），这个委托返回一个bool值表示转换是否正常。并且使用一个out参数来表示转换结果

对于像Int.TryParse这样的异常无关的转换。将会很好。但是。如果没有（比如当TryConverter转换完成的时候）。我们需要捕获返回false的异常。

这个问题并没有真正产生。这个问题我正在构造来抛出该异常。在这种情况下。异常将会被捕获。然后返回false，然后又引发另一个异常。。太恐怖了。

但是接口可以解决这个问题。通过接口我们可以定义两个方法。`Convert` 和`TryConvert`.

这样Convert可以用来进行抛出异常的转换，而TryConvert则用于不抛出异常的转换。

如果仅有一个引起一个异常的转换，那么TryConvert将会被强制捕获异常，如果有一个不引起异常，并且可能失败的转换，那么Convert将需要检查然后引起异常。但是我们要避免那种捕获一个返回false的异常。然后又抛出另一个异常的情况。

这种复杂性使得接口成为最佳解决方案。没有一个同等的委托解决方案，能够确定的比TryConvert给出更好的性能

对于那些读过我其他文章的人。你可以等Converters的更新。更新会使用接口来解决。Convert和TryConvert都会实现，作为一个无类型的接口的功能。并且CastedGet将会被淘汰。

# 结论

&nbsp;

我依旧问我自己。当一个方法接受一些参数。但只是使用一次。接口更快。委托则支持匿名。我到底该用哪一个。

对于多数情况下的事件。委托更好这毫无疑问。但是更多的情况下（通常意义上，当我注册委托并且允许用户能够找到他们的时候）我会用接口替换掉委托，因为后者支持更好的类型或无类型的支持。更易使用并且性能更好。

以一些关键点结束吧。

&nbsp;

## 委托：

1.  引用类型，因此分配一个完整的对象引用到一个方法。
2.  当你编译时就知道参数类型的使用调用最快。
3.  允许使用匿名委托，简化了单行或是小的委托的创建。
4.  可以不用创建新类型引用到一个私有的方法上。
&nbsp;

## 接口：

1.  不分配新对象。因为获得很快。
2.  对于单一用例的情况。更快。金辉创建一个对象而不是两个。
3.  如果设计的好，允许无类型的泛型使用，并且比委托的DynamicInvoke更快。
4.  如果设计的好。泛型接口可以被无类型的接口（该接口有着相同的方法签名和参数）访问，通过object改变泛型参数。
5.  允许不同的调用（比如Convert和TryConvert）
6.  对于已知的参数类型调用会慢一些。
7.  不支持编译时的匿名使用
8.  即使只需要其中的一个方法。也需要创建完整的类型。
&nbsp;

例子

&nbsp;

示例软件将会比较不同情况下的速度。

本来打算都执行1亿次迭代。但是`DynamicInvoke` 实在太慢了。所以就降到1000万了。。

&nbsp;

在我的电脑上。输出结果如下；
<div>
<pre class="lang:default decode:true">This application tests the speed of interfaces and delegates in

different situations. Compile it in release and execute it outside

Visual Studio to get the right results.

The following tests do 100 millions of iterations:

Testing delegate speed, the wrong way: 00:00:01.6483403

Testing interface speed, the wrong way: 00:00:00.5369746

Testing delegate speed, the right way: 00:00:00.3757670

Testing interface speed, the right way: 00:00:00.4831114

Testing anonymous delegate speed: 00:00:01.7475340

Testing an interface that does the same: 00:00:01.1950063

The following tests do only 10 millions of iterations:

Testing delegate's DynamicInvoke speed: 00:00:37.0368337

Testing interface's DynamicInvoke speed: 00:00:00.3218726

All the tests are finished. Press ENTER to exit.</pre>
&nbsp;

</div>

# 许可

本文包括源代码和文件在CPOL下授权。

# 下载：

[接口VS 委托](http://pan.baidu.com/share/link?shareid=75355&amp;uk=1493685990)

原文地址: [Interfaces-vs-Delegates](http://www.codeproject.com/Articles/468967/Interfaces-vs-Delegates)

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！