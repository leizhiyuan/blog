---
title: "C# 委托知识总结"
tags:
  - C＃
  - 学习
  - 开发
  - 收藏
id: 1494
categories:
  - 文章收藏
date: 2012-09-16 10:52:01
---

如果你足够强大，你就不会把幸福押在别人身上，你会自己创造幸福或者给别人带来幸福。而变得强大的途径就是学习，就是读书，学一切东西，读任何想读的书。  爱你让我变得更强。。
继续读一些专业文章了。不保证都是原创，但是保证每篇技术文章的质量。也会注明来源，标准就是浅显易懂。但不简单。

原文来自：[贺臣](http://www.cnblogs.com/qingyuan/archive/2010/05/11/1732415.html)感谢原作者的好文章。
1.什么是委托，为什么要使用委托

我正在埋头苦写程序，突然想喝水，但是又不想自己去掉杯水而打断自己的思路，于是我就想让女朋友去给我倒水。她去给我倒水，首先我得让她知道我想让她干什么，通知她之后我可以继续写自己的程序，而倒水的工作就交给了她。这样的过程就相当于一个委托。

在程序过程中，当程序正在处理某个事件的时候，我需要另外的程序代码去辅助处理一些事情，于是委托另一个程序模块去处理，而委托就可以达到这种目的，我可以利用委托通知另外的程序模块，该去调用哪个函数方法。委托其实就起到了这样一个作用，将函数签名传递到了另一个函数中。或许这样讲还是有些模糊，看看后面的具体实例。

2.委托的定义
<pre class="lang:c# decode:true " >
delegate int Add(int num1,int num2);

delegate void ConvertNum(string result);
</pre>
上面是定义两个委托的例子，其实很简单。声明一个委托使用delegate关键字，上面分别是定义的带返回值的委托和不带返回值的委托，

两个委托都有传递参数，当然也可以不传递参数。其实委托也是一个类，委托派生为System.MulticastDelegate,而System.MulticastDelegate

又继承System.Delegate,如果你知道这个也就明白委托其实是一个特殊的类。
委托的简单实用例子
<pre class="lang:c# decode:true " >
public delegate string TeaDelegate(string spText);

     public class DelegateSource
     {
         public void TestDelegate()
         {
             Operator op = new Operator();
             TeaDelegate tea = new TeaDelegate(op.GetTea);
             Console.WriteLine("去给我倒杯水");
             Console.WriteLine();
             string result=tea("去给我倒杯水");
             Thread.Sleep(5000);
             Console.WriteLine(result);
             Console.WriteLine();
         }
     }

     public class Operator
     {
         /// <summary>
         /// 确定是否还有水
         /// </summary>
         private bool flag = true;

         public string GetTea(string spText)
         {
             if (spText == "去给我倒杯水")
             {
                 if (flag)
                 {
                     return "老公,茶来了";
                 }
                 else
                 {
                     return "老公,没有水了";
                 }
             }
             return "等待.......";
         }
     }</pre>
输出结果
![](/images/)

上面使用最普通的一种方式来定义了一个委托的使用，这个例子虽然很简单，但是能够很形象的描述委托的使用。

3.委托的三种形式

(1).推断
推断委托例子
<pre class="lang:c# decode:true " >
public delegate string TeaDelegate(string spText);

     public class DelegateSource
     {
         public void TestDelegate()
         {
             Operator op = new Operator();
             TeaDelegate tea = op.GetTea;
             Console.WriteLine("去给我倒杯水");
             Console.WriteLine();
             string result=tea("去给我倒杯水");
             Thread.Sleep(5000);
             Console.WriteLine(result);
             Console.WriteLine();
         }
     }

     public class Operator
     {
         /// <summary>
         /// 确定是否还有水
         /// </summary>
         private bool flag = true;

         public string GetTea(string spText)
         {
             if (spText == "去给我倒杯水")
             {
                 if (flag)
                 {
                     return "老公,茶来了";
                 }
                 else
                 {
                     return "老公,没有水了";
                 }
             }
             return "等待.......";
         }
     }
</pre>

在委托定义的例子中我们看到委托的使用方法是在委托实例化的时候指定的[new DelegateName(FunctionName)],这里可能表述不是太但是代码应该看得白了。 而委托的推断，并没有new 委托这个步骤，而是直接将Function 指定给委托。

(2).匿名函数
匿名委托例子
<pre class="lang:c# decode:true " >
public delegate string TeaDelegate(string spText);

     public class DelegateSource
     {
         public void TestDelegate()
         {
             Operator op = new Operator();
             bool flag = true;
             TeaDelegate tea = delegate(string spText)
             {
                 if (spText == "去给我倒杯水")
                 {
                     if (flag)
                     {
                         return "老公,茶来了";
                     }
                     else
                     {
                         return "老公,没有水了";
                     }
                 }
                 return "等待.......";
             };

             Console.WriteLine("去给我倒杯水");
             Console.WriteLine();
             string result=tea("去给我倒杯水");
             Thread.Sleep(5000);
             Console.WriteLine(result);
             Console.WriteLine();
         }
     }</pre>

至于匿名委托，给人的感觉更为直接了，都不用显示的指定方法名，因为根本没有方法，而是指定的匿名方法。匿名方法在.NET 中提高了

代码的可读性和优雅性。对于更多操作较少的方法直接写为匿名函数，这样会大大提高代码的可读性。这里有两个值得注意的地方: 第一,不能使用

跳转语句跳转到该匿名方法外,第二 不能使用ref,out修饰的参数

(3).多播委托
多播委托例子
<pre class="lang:c# decode:true " >public delegate string TeaDelegate(string spText);

     public class DelegateSource
     {
         public void TestDelegate()
         {
             Operator op = new Operator();

             TeaDelegate tea1 = op.GetTea;
             TeaDelegate tea2 = op.Speak;
             TeaDelegate tea = tea1 + tea2;

             Console.WriteLine("去给我倒杯水");
             Console.WriteLine();
             string result=tea("去给我倒杯水");
             Thread.Sleep(5000);
             Console.WriteLine(result);
             Console.WriteLine();
         }
     }

     public class Operator
     {
         /// <summary>
         /// 确定是否还有水
         /// </summary>
         private bool flag = true;

         public string GetTea(string spText)
         {
             if (spText == "去给我倒杯水")
             {
                 if (flag)
                 {
                     return "老公,茶来了";
                 }
                 else
                 {
                     return "老公,没有水了";
                 }
             }
             return "等待.......";
         }

         public string Speak(string spText)
         {
             Console.WriteLine("\n去把我的设计图稿拿来");
             return null;
         }
     }</pre>

还是上面的那个实例，我不尽想让女朋友去给我掉杯水，还让她帮我将程序设计图稿拿过来。这个时候做的就不是一件事了，而是多件。

程序中也有很多这种情况，于是我们需要多播委托，在一个委托上指定多个执行方法，这是在程序中可以行的。上面提到了，委托直接继承于

System.MulticastDelegate，正是因为这个类可以实现多播委托。如果调用多播委托，就可以按顺序连续调用多个方法。为此，委托的签名就必须返回void；否则，就只能得到委托调用的最后一个方法的结果。所以在上面的这段代码中是得不到结果的

&nbsp;

4.事件

使用C#编程，无论是WinForm,WebForm 给人很难忘得就是它的控件，而他们的控件库使用方式都是使用使用事件驱动模式，而事件驱动模式却少不了委托。话不多说，看代码能够更清好的理解事件和委托之间的联系.
事件的使用
<pre class="lang:c# decode:true " >public delegate void MyDelegate(string name);

     public class EventSource
     {
         public event MyDelegate Event_Delegate;

         public void SetCustomer(string name)
         {
             Console.WriteLine("事件发生.....\n");
             Console.WriteLine("hi! "+name);
         }

         public void TestEvent()
         {
             EventSource source = new EventSource();
             Console.WriteLine("订阅事件.....\n");
             source.Event_Delegate += new MyDelegate(source.SetCustomer);
             Console.WriteLine("触发事件.....\n");
             source.Event_Delegate("hechen");
             Console.WriteLine("..................");
         }
     }</pre>
&nbsp;

上面的代码中我们定义了一个委托,然后定义了一个类EventSource，这个类中声明了一个事件。定义一个事件使用event 关键字,定义一

个event必须指定这个event传递消息的委托,在触发事件之前必需订阅事件，我们使用+= new 语法来订阅一个事件,也就相当于实例化一个事件。

当我们触发事件的时候，就会调用相应的方法去处理。

5\. 泛型委托

委托是类型安全的引用,泛型委托就和我们常用的泛型类一样，这个类在使用的时候才能确定类型.通过泛型委托，我们可以在委托传递参数

之后知道它的类型.在.NET中有一个很典型的泛型委托：
<pre class="lang:c# decode:true " >
public delegate voie EventHandler&lt;TEventArgs&gt;(object sender,TEventArgs e) where TEventArgs:EventArgs.
</pre>
这是一个非常有特色的泛型委托，可能我们用的比较少，但是作用是不能忽视的。 我们看看三个非常具有代表性的泛型委托.现在.NET4.0已经出来了，但是泛型委托.NET2.0就出来了，Linq 大家用的那叫一个甜，

为啥 函数式编程风格，匿名方法，Lamda表达式表达式使用是如此的魅力。但是大家仔细观察过没有，Linq 中的方法有几个经常出现的参数：
<pre class="lang:c# decode:true " >
Action&lt;T&gt;,Predicate&lt;T&gt;，Func&lt;T, Result&gt;
</pre>
Func&lt;T, E&gt;：封装一个具有一个参数并返回 E 参数指定的类型值的方法,T 是这个委托封装方法的参数类型，E是方法的返回值类型。当然Func&lt;T, Result&gt; 只是其中的一种情况,这个委托还有其他的几种情况:Func&lt;T&gt; 这个是方法没有参数,返回值类型是T;Func&lt;T1,T2,Result&gt; 这个方法有两个参数,类型分别为T1,T2，返回值是Result,还有Func&lt;T1,T2,T3,Result&gt;，Func&lt;T1,T2,T3,T4,Result&gt; 这几中情况，具体情况就不介绍了.我们还可以通过扩展类型，扩展为更多的参数.
Func 委托的使用
<pre class="lang:c# decode:true " >public void TestFunc()
         { 
             TEventSource eventSource=new TEventSource();
             Func<string, string> func = eventSource.GetTea;
             string result = func("茶");
             Console.WriteLine(result);
         }

         public string GetTea(string context)
         {
             if (context == "茶")
             {
                 return "茶来了";
             }
             else
             {
                 return "设计稿子来了";
             }
         }</pre>

Action&lt;T&gt;:封装一个方法，该方法只采用一个参数并且不返回值,包括Action&lt;T&gt;,Action&lt;T1,T2&gt;,Action&lt;T1,T2,T3&gt;,Action&lt;T1,T2,T3,T4&gt; 这几种情况,也可以通过扩展方法去扩展参数的个数 。
Action 委托使用例子
<pre class="lang:c# decode:true " >public void TestAction()
         {
             TEventSource eventSource = new TEventSource();
             Action<string> action = eventSource.Speak;
             action("Action<T> 泛型委托");
         }

         public void Speak(string context)
         {
             Console.WriteLine(context);
         }</pre>
&nbsp;

Predicate&lt;T&gt;:表示定义一组条件并确定指定对象是否符合这些条件的方法。该委托返回的是一个bool类型的值,如果比较满足条件

返回true,否则返回false.其实上面的Func 委托可以包含这个委托.不过这个委托和上面的两个不一样，它只有一种类型
Predicate 委托使用例子<pre class="lang:c# decode:true " >public void TestPredicate()
         {
             TEventSource eventSource = new TEventSource();
             Predicate<int> predicate = eventSource.IsRigth;
             Console.WriteLine(predicate(0));
         }

         public bool IsRigth(int value)
         {
             if (value == 0)
             {
                 return true;
             }
             else
             {
                 return false;
             }
         }</pre>

6.异步委托

投票技术: 委托其实相当于一个线程,使用投票技术是使用异步委托的一种实现方式.Delegate类提供了方法BeginInvoke()，可以传送委托类型定义的输入参数，其返回类型为IAsyncResult。IAsyncResult的IsCompleted属性可以判断委托任务是否完成
异步委托投票技术
<pre class="lang:c# decode:true " >public delegate int DelegateVote(int data, int ms);
     /// <summary>
     /// 使用投票操作完成委托任务
     /// </summary>
     public class VoteDelegate
     {
         /// <summary>
         /// 休眠特定时间执行操作
         /// </summary>
         /// <param name="data"></param>
         /// <param name="ms"></param>
         /// <returns></returns>
         public static int TakeWork(int data, int ms)
         {
             Console.WriteLine("开始调用TakeWork方法");
             Thread.Sleep(ms);
             Console.WriteLine("结束调用TakeWork方法");
             return data + 10;
         }

         public void TestDelegate()
         {
             DelegateVote voteDel = TakeWork;
             IAsyncResult result = voteDel.BeginInvoke(1,5000,null,null);
             while (result.IsCompleted == false)
             {
                 Console.WriteLine("等待......");
                 Thread.Sleep(500);
             }
             int value = voteDel.EndInvoke(result);
             Console.WriteLine("委托调用结果:  "+value);
         }
     }</pre>

等待句柄:等待句柄是使用AsyncWaitHandle属性访问，返回一个WaitHandle类型的对象，它可以等待委托线程完成其任务。在这个参数中可以设置最大的等待时间。
异步委托等待句柄
<pre class="lang:c# decode:true " > public delegate string WaitDelegate(string content);

     public class WaitHandlerDelegate
     {
         public void TestWaitHander()
         {
             WaitDelegate del = GetTea;
             IAsyncResult ar = del.BeginInvoke("hechen", null, null);
             while (true)
             {
                 Console.Write(".");
                 if (ar.AsyncWaitHandle.WaitOne(50, false))
                 {
                     break;
                 }
             }
             string result=del.EndInvoke(ar);
             Console.WriteLine(result);

         }

         public static string GetTea(string content)
         {
             return "茶来了  "+content;
         }
     }</pre>

异步回调:这个方式和投票技术有点类似，不过在投票方式中BeginInvoke()方法第三个参数指定了一个方法签名,而这个方法参数接收IAsyncResult 类型的参数。
异步委托回调函数
<pre class="lang:c# decode:true " >public delegate string AsyDelegate(string content);

     public class AsyncresultDelegate
     {
         public void TestAsync()
         {
             AsyDelegate del = GetTea;
             del.BeginInvoke("hechen", delegate(IAsyncResult ar) {
                 Thread.Sleep(5000);
                 string result = del.EndInvoke(ar);
                 Console.WriteLine(result);
             }, null);
             for (int i = 0; i < 100; i++)
             {
                 Console.WriteLine("等待.....");
                 Thread.Sleep(1000);
             }
         }

         public static string GetTea(string content)
         {
             return "茶来了  " + content;
         }
     }</pre>
&nbsp;