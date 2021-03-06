---
title: "C#多线程揭秘"
tags:
  - C＃
  - windows
  - 学习
  - 翻译
id: 2689
categories:
  - 我的翻译
date: 2013-02-12 16:31:22
---

文章略长。。。
Demo下载：[Demo.Threading.zip](http://pan.baidu.com/share/link?shareid=290200&uk=1493685990)
**介绍**
本文将通过一些例子来展示.net 中如何实现多线程，涉及到以下四部分。
1 .线程概念
2 .如何实现多线程
3 .如何确保线程安全
4 .死锁

**什么是进程**
一个进程就是一个可执行文件运行的操作系统上下文环境。它被用来分隔虚拟地址空间，线程，对象句柄（指向类似文件这样的资源的指针），以及环境变量，进程还有一些类似优先级类和最大内存分配的属性。

也就是说：
1 .一个进程就是一个包含资源的内存块。
2 .操作系统执行的一个单独的任务。
3 .一个正在运行的软件
4 .一个进程拥有一个/多个操作系统线程

一般的。一个进程最大可以是4GB的内存空间，这块内存是安全，私有，其他进程是无法访问的。

**什么是线程**
一个线程就是在一个进程里执行的一条指令流，所有的线程都在一个进程里执行，也就是一个进程可以包含多个线程。线程公用进程的虚拟地址空间。线程是操作系统的调度单元。一个线程的上下文由操作系统进行保存/恢复。
也就是说：
1 .一个线程是进程里的一条指令流。
2 .所有的线程在进程里。一个进程可以有多个线程
3 .一个进程的所有线程使用进程的虚拟地址空间。

**什么是多线程**
多线程指的是进程同时有多个线程活动。这可以通过时间片的线程模拟或是多cpu上的超线程来实现。可以提高性能。
**多线程-为什么或是为什么不?**
为什么多线程
1 .保持UI响应。
2 .提高性能(对于cpu密集型和I/O密集型的进程)
为什么不多线程
1 .过度使用降低性能
2 .代码复杂，增加设计时间，潜在的bug

线程池
线程池为你的程序提供了一个由操作系统管理的机制。在线程池里的都是后台线程。一个线程池线程在程序的前台线程都退出后，也会推出。每个进程一个线程池。默认情况下。每个处理器会为进程分配25个线程。但是可以通过SetMaxThreads  方法来改变。

**.net 中的线程**
在.net 中，线程可以通过下面6个方法来实现。
1 .Thread线程类
2 .Delegates委托
3 .Background Worker
4 .ThreadPool 线程池
5 .Task任务类
6 .Parallel并行类

下面的几部分里。我将逐一展示实现方法。

简而言之，多线程就是通过使程序同时运行多个任务来最大化计算机能力，同时能够保持UI响应。下图是一个例子的图示。
 [![](/images/472b9c05527afb3cb133d1d5689cdca367412bc0.jpg)](http://leaverimage.b0.upaiyun.com/32605_o.jpg)

**代码**
提供的源码是一个简单的WinForm程序。模拟了.net中委托，线程类和Background Worker三种方法。
程序异步执行一个繁重的操作，这样UI就不会无响应。三个方法都是模拟的。
 [![](/images/a0567e4556b306284f9d9793d2879ae6716dae23.jpg)](http://leaverimage.b0.upaiyun.com/32606_o.jpg)

这个“繁重”的操作
真实的开发中，这个繁重的操作从轮询数据库到流媒体操作都可以。基本上可以是任何事情。源码里面是向一个字符串追加值。String是不能变的。追加的时候，新的字符串变量会被创建，旧的会被丢弃，这是由CLR处理的。如果做很多次这个操作，是很耗资源的。这也是为什么我们使用Stringbuilder.Append 来代替这个操作。通过调整界面中的次数。可以通知追加的次数。

后面我们有一个Utility泪，有一个LoadData() 方法。类里面也有一个和LoadData() 有着同样签名的委托

<pre class="lang:default decode:true " >class Utility
{
    public delegate string delLoadData(int number);
    public static delLoadData dLoadData;

    public Utility()
    {

    }

    public static string LoadData(int max)
    {
        string str = string.Empty;

        for (int i = 0; i &lt; max; i++)
                                {
            str += i.ToString();
                                }

        return str;
    }
}
</pre> 

**同步调用**
当点击Get Data Sync按钮的时候。操作和UI在同一个线程里，因此阻塞了UI线程。因此。UI线程会未响应

<pre class="lang:default decode:true " >private void btnSync_Click(object sender, EventArgs e)
{
    this.Cursor = Cursors.WaitCursor;
    this.txtContents.Text = Utility.LoadData(upCount);
    this.Cursor = Cursors.Default;
}
</pre> 

**异步调用**
使用委托（异步编程模型）

如果你选择了“Delegates”单选按钮，那么LoadData()方法就会通过使用委托来异步调用。首先通过utility.LoadData(). 的地址初始化delLoadData  类型，然后调用委托的BeginInvoke()方法。在.net的世界里。任何一个有着BeginXXX和EndXXX名字的方法都是异步的。比如delegate.Invoke()将会在同一个线程里调用方法。而delegate.BeginInvoke()则会另开一个线程调用。
BeginInvoke()有三个参数
1 .传递给Utility.LoadData()方法的参数
2 .回调方法的地址
3 .对象的状态

<pre class="lang:default decode:true " >Utility.dLoadData = new Utility.delLoadData(Utility.LoadData);
Utility.dLoadData.BeginInvoke(upCount, CallBack, null);</pre> 

**回调**
一旦我们开了一个线程执行一些操作，我们就想知道操作正在发生些什么，换句话说。我们需要当操作完成的时候我们能够收到通知。有三种方法可以知道一个操作是否完成。
1 .回调
2 .轮询
3 .等待直到完成
在我的源码里，我们使用回调方法来捕获线程的完成。回调只需要在调用BeginInvoke的时候把回到函数的名字传递进去。这会告诉线程当你做完工作以后调用这个回调方法就好了。

一旦一个独立线程里的一个方法启动。你也许关心也许不关心方法的返回值，如果一个方法没有返回值，那么可以叫做“触发然后忘记的调用”，这种情况下就不需要回调函数了。这里callback直接传入null就可以了。

<pre class="lang:default decode:true " >Utility.dLoadData.BeginInvoke(upCount, CallBack, null);</pre> 

在我们的例子中，我们需要一个回调方法，因此，哦们需要传递回调方法的名字到参数里。这里我们的回调方法的名字就叫做CallBack(),纯属巧合。

<pre class="lang:default decode:true " >private void CallBack(IAsyncResult asyncResult)
{
    string result= string.Empty;

    if (this.cancelled)
        result = "Operation Cancelled";
    else
        result = Utility.dLoadData.EndInvoke(asyncResult);

      object[] args = { this.cancelled, result };
    this.BeginInvoke(dUpdateUI, args);
}
</pre> 

回调方法的签名都是void MethodName(IAsyncResult asyncResult).
IAsyncResult包含了关于线程的一些必要的信息，返回的数据可以像下面这样提取。

<pre class="lang:default decode:true " >result = Utility.dLoadData.EndInvoke(asyncResult);</pre> 

而轮询的方法（本例没有使用）则是像这样

<pre class="lang:default decode:true " >IAsyncResult r = Utility.dLoadData.BeginInvoke(upCount, CallBack, null);
while (!r.IsCompleted)
{
    //do work
}
result = Utility.dLoadData.EndInvoke(asyncResult);</pre> 

等待直到完成，如名所示，就是等待直到完成。

<pre class="lang:default decode:true " >IAsyncResult r = Utility.dLoadData.BeginInvoke(upCount, CallBack, null);

//do work
result = Utility.dLoadData.EndInvoke(asyncResult);</pre> 

更新UI
既然我们已经捕获了操作结束，并且取回了LoadData()的结果。我们需要用结果来更新UI，但是有个问题。文本框需要在UI线程里更新，结果在回调里取到了。回调和他启动的时候是一个线程(他是由新开的线程启动的)。因为UI线程和回调不是同一个线程。换句话说。文本框不能像下面这样更新。

<pre class="lang:default decode:true " >this.txtContents.Text = text;</pre> 

回调里执行这一行将会导致一个跨线程的系统异常。我们需要在后台线程和Ui线程之前构建一个桥。来更新文本框的值。可以通过使用Invoke()或是BeginInvoke()方法。
我定义了一个方法来更新UI

<pre class="lang:default decode:true " >private void UpdateUI(bool cancelled, string text)
{
    this.btnAsync.Enabled = true;
    this.btnCancel.Enabled = false;
    this.txtContents.Text = text;
}</pre> 

对上面的方法定义一个委托

<pre class="lang:default decode:true " >private delegate void delUpdateUI(bool value, string text);
dUpdateUI = new delUpdateUI(UpdateUI);</pre> 

如下调用BeginInvoke()方法。

<pre class="lang:default decode:true " >object[] args = { this.cancelled, result };
this.BeginInvoke(dUpdateUI, args);</pre> 

需要注意的是一旦一个线程通过委托启动。它就不能取消，暂停，或是终止，我们无法控制那个线程。

使用Thread线程类
同样的操作可以是哟哦那个Thread类来完成。这个类的优点是你可以对操作有更多的控制，比如暂停/取消操作，类在System.Threading命名空间里。
我们有一个私有的方法LoadData(),他是Utility.LoadData()方法的一个包装。

<pre class="lang:default decode:true " >private void LoadData()
{
    string result = Utility.LoadData(upCount);
    object[] args = { this.cancelled, result };
    this.BeginInvoke(dUpdateUI, args);
}</pre> 

这样做是因为 Utility.LoadData() 需要一个参数。而我们需要一个ThreadStart委托，这个委托没有参数。

<pre class="lang:default decode:true " >doWork = new Thread(new ThreadStart(this.LoadData));
doWork.Start();</pre> 

这个委托没有参数，为了防止我们需要传递参数，我们可以使用有参的ThreadStart委托，不幸的是，这个委托只能把object作为参数，而我们需要一个字符串所以需要类型转换。

<pre class="lang:default decode:true " >doWork = new Thread(new ParameterizedThreadStart(this.LoadData));
doWork.Start(parameter);</pre> 

是的。Thread泪可以对线程有更多的控制。中断。终止，获取线程状态。
使用BackgroundWorker
这个类是一个组件，可以使得线程使用更简单，这个BackgroundWorker类的主要特点就是可以异步的报告进度，这就可以用来更新状态栏，保持UI可视化的更新进度
为了完成操作，我们需要把下面两个属性设置为true，缺省时false
•	WorkerReportsProgress
•	WorkerSupportsCancel

这个类有三个主要的事件DoCount, ProgressChanged, RunWorkerCompleted 初始化的时候需要注册这三个事件

<pre class="lang:default decode:true " >this.bgCount.DoWork += new DoWorkEventHandler(bgCount_DoWork);
this.bgCount.ProgressChanged += 
     new ProgressChangedEventHandler(bgCount_ProgressChanged);
this.bgCount.RunWorkerCompleted += 
     new RunWorkerCompletedEventHandler(bgCount_RunWorkerCompleted);</pre> 

通过调用RunWorkerAsync() 方法来启动操作

<pre class="lang:default decode:true " >this.bgCount.RunWorkerAsync();</pre> 

一旦调用，下面的方法就会启动来执行操作。

<pre class="lang:default decode:true " >void bgCount_DoWork(object sender, DoWorkEventArgs e)
{
    string result = string.Empty;
    if (this.bgCount.CancellationPending)
    {
        e.Cancel = true;
        e.Result = "Operation Cancelled";
    }
    else
    {
        for (int i = 0; i &lt; this.upCount; i++)
        {
            result += i.ToString();
            this.bgCount.ReportProgress((i / this.upCount) * 100);
        }
        e.Result = result;
    }
}
</pre> 

CancellationPending  属性用来检查该操作是否被取消。要取消操作，需要调用

<pre class="lang:default decode:true " >this.bgCount.CancelAsync();</pre> 

下面这行代码报告进度

<pre class="lang:default decode:true " >this.bgCount.ReportProgress((i / this.upCount) * 100);</pre> 

一旦调用，下面的方法就会被调用，来更新UI

<pre class="lang:default decode:true " >void bgCount_ProgressChanged(object sender, ProgressChangedEventArgs e)
{
    if (this.bgCount.CancellationPending)
        this.txtContents.Text = "Cancelling....";
    else
        this.progressBar.Value = e.ProgressPercentage;
}</pre> 

最后，操作完成时调用bgCount_RunWorkerCompleted  方法

<pre class="lang:default decode:true " >void bgCount_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
    this.btnAsync.Enabled = true;
    this.btnCancel.Enabled = false;
    this.txtContents.Text = e.Result.ToString();
} 
</pre> 

Thread Pool线程池

不建议程序员随心所欲创建很多线程，创建线程是很昂贵的操作，有一些额外的调用。同时，每个cpu在一个时间片内只能运行一个建成，如果一个单核系统上有多个线程，计算机一次只能运行一个，因此通过给线程分配时间片来模拟多线程。会产生上下文切换的消费，因此，如果有很多的线程，其中一些什么都不做，保持闲置，那么这些额外的消费会影响性能。因此，程序员对于创建线程应该相当小心

幸运的是，CLR有一个托管的代码库。这就是ThreadPool类，这个类管理一些线程，并且根据我们的程序创建或销毁线程，开始的时候没有线程启动。当需要的时候就会创建，如果我们设置了SetMinThreads属性，一旦开始操作就很快会达到这个值，之后，如果发现有些线程闲置了很长时间，则会决定会杀掉一些线程。
线程池类液允许我们管理一系列的工作项目。这些工作项目会委托到一个后台线程。

<pre class="lang:default decode:true " >WaitCallback threadCallback = new WaitCallback(HeavyOperation);

for (int i = 0; i &lt; 3; i++)
{
  System.Threading.ThreadPool.QueueUserWorkItem(HeavyOperation, i);			 
}</pre> 

其中heavyOperation定义如下：

<pre class="lang:default decode:true " >private static void HeavyOperation(object WorkItem)
{
  System.Threading.Thread.Sleep(5000);
  Console.WriteLine("Executed work Item {0}", (int)WorkItem);
} </pre> 

注意WaitCallBack这个委托的签名，需要把一个object作为参数，通常用来在线程间传递状态信息。
注意我们知道委托通过使用ThreadPool来工作。我们必须探索和他一起的回调技术，我们可以使用WaitHandle来捕获回调，WaitHandle派生了两个子类：
 **AutoResetEvent 和 ManualResetEvent. **

<pre class="lang:default decode:true " >public static void Demo_ResetEvent()
{  
  Server s = new Server();
  ThreadPool.QueueUserWorkItem(new WaitCallback((o) =&gt;
  {
     s.DoWork();                

   }));

   ((AutoResetEvent)Global.GetHandle(Handles.AutoResetEvent)).WaitOne();
    Console.WriteLine("Work complete signal received");
} </pre> 

这里有一个Global类，这个类包含WaitHandles的一个单例。

<pre class="lang:default decode:true " >public static class Global
{
  static WaitHandle w = null;
  static AutoResetEvent ae = new AutoResetEvent(false);
  static ManualResetEvent me = new ManualResetEvent(false);
  public static WaitHandle GetHandle(Handles Type)
  {            
    switch (Type)
    {                
      case Handles.ManualResetEvent:                    
         w = me;
         break;
      case Handles.AutoResetEvent:                    
         w = ae;                    
         break;
      default:
         break;
    }
    return w;
  }
}  </pre> 

而WaitOne方法。阻塞了代码执行，直到在后台线程中设置了WaitHandle。

<pre class="lang:default decode:true " >public void DoWork()
{            
  Console.WriteLine("Work Starting ...");
  Thread.Sleep(5000);
  Console.WriteLine("Work Ended ...");
  ((AutoResetEvent)Global.GetHandle(Handles.AutoResetEvent)).Set();
} </pre> 

AutoResetEvent当自动设置以后又重设自己。和高速收费站很类似。多辆车合并，以让一次只有一辆车通过。当一辆车来的时候，门就设置为允许通过，然后又重设为关闭处理下一辆车。
下面的例子详细说明了AutoResetEvent。想一想。我们有一个服务名为DoWork()这个方法就是繁重的操作，我们的程序需要在调用这个方法后更新日志文件。考虑到多个线程异步的访问这个方法。我们必须确保更新日志文件是线程安全的，这样一次只能有一个线程可用。

<pre class="lang:default decode:true " >public void DoWork(int threadID, int waitSingal)
{ 
  Thread.Sleep(waitSingal);
  Console.WriteLine("Work Complete by Thread : {0} @ {1}", threadID, DateTime.Now.ToString("hh:mm:ss"));
  ((AutoResetEvent)Global.GetHandle(Handles.AutoResetEvent)).Set();

} 

public void UpdateLog(int threadID)
{
  if(((AutoResetEvent)Global.GetHandle(Handles.AutoResetEvent)).WaitOne(5000))
       Console.WriteLine("Update Log File by thread : {0} @ {1}", threadID, DateTime.Now.ToString("hh:mm:ss"));
  else
       Console.WriteLine("Time out");
}</pre> 

我们创建两个线程，同时委托DoWork()方法。然后我们调用UpdateLog()方法。更新日志的代码执行将会等待直到每一个线程都完成各自的工作之后才执行。

<pre class="lang:default decode:true " >public static void Demo_AutoResetEvent()
{
  Console.WriteLine("Demo Autoreset event...");
  Server s = new Server();

  Console.WriteLine("Start Thread 1..");
  ThreadPool.QueueUserWorkItem(new WaitCallback((o) =&gt;
  {
     s.DoWork(1, 4000);  

  }));            

  Console.WriteLine("Start Thread 2..");
  ThreadPool.QueueUserWorkItem(new WaitCallback((o) =&gt;
  {
     s.DoWork(2, 4000);                

  }));

  s.UpdateLog(1);
  s.UpdateLog(2);
} 
</pre> 

ManualResetEvent 不同于AutoResetEvent，我们需要在再次设置之前手工重置他，他不会自动重置，考虑我们有一个方法是持续在后台线程中发布消息。这个方法持续循环等待信号以发送消息。当值被设置以后，方法就开始发消息。当等待句柄被重置，发送服务停止，然后处理就可以重复进行了。

<pre class="lang:default decode:true " >public void SendMessages(bool monitorSingal)
{            
  int counter=1;
  while (monitorSingal)
  {
     if (((ManualResetEvent)Global.GetHandle(Handles.ManualResetEvent)).WaitOne())
     {
        Console.WriteLine("Sending message {0}", counter);
        Thread.Sleep(3000);
        counter += 1;
     }
  }           
} 

public static void Demo_ManualResetEvent()
{
  Console.WriteLine("Demo Mnaulreset event...");
  Server s = new Server();
  ThreadPool.QueueUserWorkItem(new WaitCallback((o) =&gt;
  {
    s.SendMessages(true);
  }));

  Console.WriteLine("Press 1 to send messages");
  Console.WriteLine("Prress 2 to stop messages");

  while (true)
  {               
    int input = Convert.ToInt16(Console.ReadLine());                              

    switch (input)
    {
      case 1:
         Console.WriteLine("Starting to send message ...");                        
         ((ManualResetEvent)Global.GetHandle(Handles.ManualResetEvent)).Set();
         break;
      case 2:                                                
         ((ManualResetEvent)Global.GetHandle(Handles.ManualResetEvent)).Reset();
         Console.WriteLine("Message Stopped ..."); 
         break;
      default:
         Console.WriteLine("Invalid Input");
         break;
    }
  }            
} </pre> 

任务Task类
.net 4.0 提出了Task，是ThreadPool的扩展，概念相当美好。我们可以取消任务，等待任务。检查进度，考虑下面的例子将要用到的三个方法。

<pre class="lang:default decode:true " >static void DoHeavyWork(CancellationToken ct)
{
 try
 {
                while (true)
                {
                    ct.ThrowIfCancellationRequested();
                    Console.WriteLine("Background thread working for task 3..");
                    Thread.Sleep(2000);
                    if (ct.IsCancellationRequested)
                    {
                        ct.ThrowIfCancellationRequested();
                    }
                }
 }
catch (OperationCanceledException ex)
    {
                Console.WriteLine("Exception :" + ex.Message);
       }
 catch (Exception ex)
       {
                Console.WriteLine("Exception :", ex.Message);
        }            

}

static void DoHeavyWork(int n)
{
  Thread.Sleep(5000);
  Console.WriteLine("Operation complete for thread {0}", Thread.CurrentThread.ManagedThreadId);
}

static int DoHeavyWorkWithResult(int num)
{
  Thread.Sleep(5000);
  Console.WriteLine("Operation complete for thread {0}", Thread.CurrentThread.ManagedThreadId);
  return num;
}
</pre> 

我们还有三个task用来运行这三个方法。第一个线程完成没有返回结果，第二个线程完成并且返回结果，第三个线程在完成之前取消。

<pre class="lang:default decode:true " >try
            {
                Console.WriteLine(DateTime.Now);
                CancellationTokenSource cts1 = new CancellationTokenSource();
                CancellationTokenSource cts2 = new CancellationTokenSource();
                CancellationTokenSource cts3 = new CancellationTokenSource();

                Task t1 = new Task((o) =&gt; DoHeavyWork(2), cts1.Token);

                Console.WriteLine("Starting Task 1");
                Console.WriteLine("Thread1 state {0}", t1.Status);
                t1.Start();

                Console.WriteLine("Starting Task 2");
                Task&lt;int&gt; t2 = Task&lt;int&gt;.Factory.StartNew((o) =&gt; DoHeavyWorkWithResult(2), cts2.Token);

                Console.WriteLine("Starting Task 3");
                Task t3 = new Task((o) =&gt; DoHeavyWork(cts3.Token), cts3);
                t3.Start();               

                Console.WriteLine("Thread1 state {0}", t1.Status);
                Console.WriteLine("Thread2 state {0}", t2.Status);
                Console.WriteLine("Thread3 state {0}", t3.Status);

                // wait for task 1 to be over
                t1.Wait();

                Console.WriteLine("Task 1 complete");

                Console.WriteLine("Thread1 state {0}", t1.Status);
                Console.WriteLine("Thread2 state {0}", t2.Status);
                Console.WriteLine("Thread3 state {0}", t3.Status);

                //cancel task 3
                Console.WriteLine("Task 3 is : {0} and cancelling...", t3.Status);
                cts3.Cancel();

                // wait for task 2 to be over
                t2.Wait();

                Console.WriteLine("Task 2 complete");

                Console.WriteLine("Thread1 state {0}", t1.Status);
                Console.WriteLine("Thread2 state {0}", t2.Status);
                Console.WriteLine("Thread3 state {0}", t3.Status);

                Console.WriteLine("Result {0}", t2.Result);
                Console.WriteLine(DateTime.Now);

                t3.Wait();

                Console.WriteLine("Task 3 complete");
                Console.WriteLine(DateTime.Now);
            }

            catch (Exception ex)
            {
                Console.WriteLine("Exception : " + ex.Message.ToString());
            }
            finally
            {
                Console.Read();
            }</pre> 

.net 4.0中并行Parallel编程（时间片）
.net 4.0提出了一个并行编程的很不错的特性，我们前面所说的大部分线程的例子都是把大量的工作交给空闲线程去做。计算机仍然一次处理一个线程。简而言之就是，不是真正的多任务执行，而通过Parallel类这就是可能的。
考虑一个Employee类，这个类有一个繁重的操作：ProcessEmployeeInformation

<pre class="lang:default decode:true " >class Employee
{
  public Employee(){}

  public int EmployeeID {get;set;}

  public void ProcessEmployeeInformation()
  {
    Thread.Sleep(5000);
    Console.WriteLine("Processed Information for Employee {0}",EmployeeID);
  }
} </pre> 

我们创建8个对象，来模拟并行请求，在一个4核的处理器上，4个请求将会同时进行，其余的则会等待。

<pre class="lang:default decode:true " >List&lt;employee&gt; empList = new List&lt;employee&gt;()
 {
   new Employee(){EmployeeID=1},
   new Employee(){EmployeeID=2},
   new Employee(){EmployeeID=3},
   new Employee(){EmployeeID=4},
   new Employee(){EmployeeID=5},
   new Employee(){EmployeeID=6},
   new Employee(){EmployeeID=7},
   new Employee(){EmployeeID=8},
 };

 Console.WriteLine("Start Operation {0}", DateTime.Now);
 System.Threading.Tasks.Parallel.ForEach(empList, (e) =&gt;e.ProcessEmployeeInformation());</pre> 

我们可以通过设置MaxDegreeOfParallelism  的值来控制/限制并行任务的数量。如果被设置为-1，就是说没有限制。。

<pre class="lang:default decode:true " >System.Threading.Tasks.Parallel.For(0, 8, new ParallelOptions() { MaxDegreeOfParallelism = 4 }, (o) =&gt;
       {
          Thread.Sleep(5000);
          Console.WriteLine("Thread ID - {0}", Thread.CurrentThread.ManagedThreadId);
        });</pre> 

并行的问题是如果我们开启了一系列请求，我们不能保持响应也是一样的顺序，顺序是不确定的。而AsOrdered属性可以帮助我们，输入可以是任何顺序，输出就是对应的顺序。

<pre class="lang:default decode:true " >Console.WriteLine("Start Operation {0}", DateTime.Now);
var q = from e in empList.AsParallel().AsOrdered()
        select new { ID = e.EmployeeID };

foreach (var item in q)
{
  Console.WriteLine(item.ID);
}
Console.WriteLine("End Operation {0}", DateTime.Now);
</pre> 

**线程安全**
关于线程常常讨论的一个就是线程安全了。考虑一个被多个线程使用的资源，资源将会以一种不确定的方式被使用，导致结果乱七八糟，这就是我们为什么要实现线程安全的程序，是为了让资源一次只能被一个线程操作，下面是.net中实现线程安全的一些方法。
Interlocked  这个Interlocked类把操作看作是原子的。比如，简单的加减法在处理器内部是分为三步的。当多个线程访问同样的对象进行这些操作的时候，导致结果混乱，一个建成在执行了前两步后，被挂起。另一个线程执行了完整的三步，之后，当第一个线程恢复执行的时候他就覆写了这个值，第二个线程所做的操作就丢失了。因此我们需要看这些操作看作是原子的。使他们能够线程安全的。比如加减，读，交换等等。
System.Threading.Interlocked.Increment(object);

Monitor  这个Monitor类用来锁住那些有可能多线程下有风险的对象。

<pre class="lang:default decode:true " >if (Monitor.TryEnter(this, 300)) {
    try {
        // code protected by the Monitor here.
    }
    finally {
        Monitor.Exit(this);
    }
}
else {
    // Code if the attempt times out.
}</pre> 

Locks  这个Locks类是Monitor的加强版，最好的一个例子就就是单例类的GetInstance() 方法，多个线程可以访问这段代码，因此使用一个syncLock对象锁住，这个对象和真实世界的锁很想，如果两个或多个资源都有要是，他们可以打开锁并且访问资源。因此，我们必须确保要是是唯一不共享的。这里就是这个syncLock对象。把这个对象作为私有的变量是很好的。

<pre class="lang:default decode:true " >static object syncLock = new object();

if (_instance == null)
{
    lock (syncLock)
    {
        if (_instance == null)
        {
            _instance = new LoadBalancer();
        }
    }
} </pre> 

Reader-Writer Lock  这个锁可以被无限制数量的同时读者请求，或者被一个单一的写者请求，如果大多数是读请求很少/时间很短，那么 比Monitor性能更好。读写者在不同的队列里，当写者拥有锁的时候，读者排队等待写者完成，当读者有锁的时候，所有的写者排队。读者和写者交替着完成工作，下面的代码详细解释了。有两个方法。ReadFromCollection  和WriteToCollection 从一个集合里各自的读/写。注意AcquireReaderLock  和 AcquireWriterLock 的使用

<pre class="lang:default decode:true " >static void Main(string[] args)
        {
            // Thread 1 writing
            new Thread(new ThreadStart(() =&gt;
                {
                    WriteToCollection(new int[]{1,2,3});

                })).Start();

            // Thread 2 Reading
            new Thread(new ThreadStart(() =&gt;
            {
                ReadFromCollection();                
            })).Start();

            // Thread 3 Writing
            new Thread(new ThreadStart(() =&gt;
            {
                WriteToCollection(new int[] { 4, 5, 6 });

            })).Start();

            // Thread 4 Reading
            new Thread(new ThreadStart(() =&gt;
            {
                ReadFromCollection();
            })).Start();            

            Console.ReadLine();
        }

        static void ReadFromCollection()
        {
            rwLock.AcquireReaderLock(5000);
            try 
            {
                Console.WriteLine("Read Lock acquired by thread : {0}  @ {1}", Thread.CurrentThread.ManagedThreadId, DateTime.Now.ToString("hh:mm:ss"));
                Console.Write("Collection : ");
                foreach (int item in myCollection)
                {
                    Console.Write(item + ", ");
                }
                Console.Write("\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception : " + ex.Message);
            }
            finally
            {
                Console.WriteLine("Read Lock released by thread : {0}  @ {1}", Thread.CurrentThread.ManagedThreadId, DateTime.Now.ToString("hh:mm:ss"));
                rwLock.ReleaseReaderLock();

            }
        }

        static void WriteToCollection(int[] num)
        {
            rwLock.AcquireWriterLock(5000);
            try
            {
                Console.WriteLine("Write Lock acquired by thread : {0}  @ {1}", Thread.CurrentThread.ManagedThreadId, DateTime.Now.ToString("hh:mm:ss"));
                myCollection.AddRange(num);
                Console.WriteLine("Written to collection ............: {0}", DateTime.Now.ToString("hh:mm:ss"));
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception : " + ex.Message);
            }
            finally
            {
                Console.WriteLine("Write Lock released by thread : {0}  @ {1}", Thread.CurrentThread.ManagedThreadId, DateTime.Now.ToString("hh:mm:ss"));
                rwLock.ReleaseWriterLock();                
            }
        }   
</pre> 

Mutex   Mutex通常用来在操作系统中共享资源，最好的例子就是检测是否同时有两个同样的进程在运行。

**死锁**
当谈论线程安全的时候，死锁是无法逃避的。
死锁是两个/多个线程锁住了同样的资源。都在等待对方释放。会导致操作无休止的等待。死锁可以通过认真的编程避免。比如：
线程A锁住对象A
线程A锁住对象B
线程B锁住对象B
线程B锁住对象A
线程A等待线程B释放对象B，而线程B等待线程A释放对象A，考虑下面的例子。在一个死锁类里，我们有两个方法OperationA和OperationB嵌套的锁住两个对象，同时运行两个方法。会导致死锁。

<pre class="lang:default decode:true " >public class DeadLock
{        
 static object lockA = new object();
 static object lockB = new object();

 public void OperationA()
 {            
  lock (lockA)
  {
   Console.WriteLine("Thread {0} has locked Obect A", Thread.CurrentThread.ManagedThreadId);
   lock (lockB)
   {
    Console.WriteLine("Thread {0} has locked Obect B", Thread.CurrentThread.ManagedThreadId);
   }
   Console.WriteLine("Thread {0} has released Obect B", Thread.CurrentThread.ManagedThreadId);
  }
  Console.WriteLine("Thread {0} has released Obect A", Thread.CurrentThread.ManagedThreadId);
 }

 public void OperationB()
 {            
  lock (lockB)
  {
   Console.WriteLine("Thread {0} has locked Obect B", Thread.CurrentThread.ManagedThreadId);
   lock (lockA)
   {
    Console.WriteLine("Thread {0} has locked Obect A", Thread.CurrentThread.ManagedThreadId);
   }
   Console.WriteLine("Thread {0} has released Obect A", Thread.CurrentThread.ManagedThreadId);
  }
  Console.WriteLine("Thread {0} has released Obect B", Thread.CurrentThread.ManagedThreadId); 
} 
}  

DeadLock deadLock = new DeadLock();

 Thread tA = new Thread(new ThreadStart(deadLock.OperationA));
 Thread tB = new Thread(new ThreadStart(deadLock.OperationB));

 Console.WriteLine("Starting Thread A");
 tA.Start();

 Console.WriteLine("Starting Thread B");
 tB.Start();
</pre> 

工作线程VS I/O线程
操作系统只有一个线程概念，但是.net 对我们抽象出了一层，我们可以处理两个线程-工作线程和I/O线程，ThreadPool.GetAvailableThreads(out workerThread, out ioThread) 这个方法可以返回给我们可用的每种线程的可用数目。当写代码的时候，程序中繁重的任务应该被分为两部分，计算密集型和I/O密集型。计算密集型是那些CPU运转较多，比如运行查询或是复杂的算法的部分。I/O密集型是那些被用来做一些系统I/O硬件或是网络设备的部分。比如-读写文件，从数据库取数据，查询远程web服务器等。计算密集型应该委托给工作线程，I/O密集型应该被委托给I/O线程。如果我们委托工作线程去做I/O密集型的操作，当设备做这个操作的时候，线程会阻塞，阻塞的线程就是浪费的资源，另一方面，如果我们使用I/O线程做同样的任务，调用线程将会委托任务给设备驱动，自己则回到线程池，当操作完成后，调用线程会从线程池中被通知来处理任务完成。有点事线程保持未阻塞来处理其他的任务，因为当调用线程发起了I/O操作以后，就委托给了操作系统的部分。来处理设备驱动。因此就没有理由阻塞线程了。在.net的类库里。有专门的类型处理I/O线程。比如FileStream类里的BeginRead()和EndRead()方法。所有类似的

**总结**
能力越大，责任越大 –线程池
1 .没有程序应该在UI线程里做繁重的任务。没有比无响应的UI更难以接受的了。一般情况下，通过使用线程池来管理线程异步执行一些繁重的任务。
2 .UI不能直接在非UI或是后台线程里更新。程序要需要委托这类工作给UI线程。这可以通过在winform里使用Invoke方法。在WPF里使用Dispatcher方法。或是使用BackGroundWorker自动处理。
3 .线程是很昂贵的资源应该被认真对待。“越多越热闹”显然是不能接受的。
4 .在我们的程序里的问题不会通过简单的把工作交给另一个线程就能解决。不会有神奇的事情发生。程序需要被合理的设计。来获得高效率。
5 .通过Thread类来创建县城的时候要万分小心。调整线程优先级应该慎重。又可以导致其他重要的线程不能执行。
6 .胡乱设置IsBackground为false可能会引发无法预料的错误。前台线程直到完成才会让程序终止，如果用户想要终止将程序，结果运行在后台的一个任务被设置为了前台线程。导致程序无法终止了。
7 .多程序中多线程贡献资源的时候，异步线程技术要小心，死锁可以通过认真的编码避免一部分。
8 .程序员应该确保线程不要太多。闲置线程可能增加开销并且导致内存溢出。
9 .I/O操作必须委托给I/O线程而不是工作线程。