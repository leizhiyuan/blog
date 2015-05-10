title: .net显示网络连接状态图标
tags:
  - C＃
  - windows
  - 原创
  - 大学
  - 翻译
  - 软件
id: 1557
categories:
  - 我的翻译
date: 2012-09-24 08:04:02
---

效果图：
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/ "internet connection")](http://leaverimage.b0.upaiyun.com/27419_o.jpg) 
=======
[![](/images/ "internet connection")](http://leaverimage.b0.upaiyun.com/27419_o.jpg) 
>>>>>>> 换电脑之后重新备份

### 介绍

　　越来越多的软件要通过连接互联网来执行一些业务层的业务操作，比如调用web services服务，获取数据等等。
通常你可能希望知道当前你的网络连接是不是真的连上了，当然有很多种方法可以做到，比如，你可以查看System.Net 命名空间中的NetworkInterface 的状态，但是有这以太网连接并不表示你的连接真的可以用。 本文将会展示一种方法，该方法在程序的状态栏StatusStrip 显示一个简单的图标来指示是不是真的连接到了互联网。

### 使用代码

　　最简单的我们会想到使用一个Timer来进行http-get请求来判断一个特定的网页是否可用。

　　当然这种方法下，我们最应该考虑的就是请求不能阻塞UI线程，因此，我将使用一个BackgroundWorker 对象来进行get请求，BackgroundWorker 对象声明了DoWork方法。该方法定义了一个事件句柄，该句柄传递一个DoWorkEventArgs 类来将事件的处理结果返回到UI线程，因此，你不必与任何的UI元素进行交互，因为它运行在一个独立的线程里。

<pre class="lang:c# decode:true " >private void InitializeComponent()
{
    // Background Worker
    this._worker = new BackgroundWorker();
    this._worker.WorkerReportsProgress = false;
    this._worker.WorkerSupportsCancellation = false;
    this._worker.DoWork += new 
       DoWorkEventHandler(this.BackgroundWorker_DoWork);
    this._worker.RunWorkerCompleted += new 
       RunWorkerCompletedEventHandler(this.BackgroundWorker_RunWorkerCompleted);

    // Timer
    this._updateTimer = new Timer();
    this._updateTimer.Enabled = !this.DesignMode;
    // Enabled when not in design mode

    this._updateTimer.Tick += delegate { this.OnTick(); };
}

private void OnTick()
{
    if (this.DesignMode)
        return;

    // Stop the timer while the process is running
    this._updateTimer.Enabled = false;

    // Disable so we get the grayed-out look
    this.Enabled = false;
    this.Invalidate();

    // Execute the Ping Query on a separate thread...
    this._worker.RunWorkerAsync();
}</pre> 

　　这个查询很简单，我执行简单的HttpWebRequest 来请求一个必然是可用的网页。比如微软的主页或是Google的主页。通过这样，我们就能知道是不是真的连接上了互联网。

<pre class="lang:c# decode:true " >private void BackgroundWorker_DoWork(object sender, DoWorkEventArgs e)
{
    try
    {
        // Create an HTTP Web request
        // to an Uri that's always available.
        HttpWebRequest request = (HttpWebRequest) 
           HttpWebRequest.Create(this._alwaysAvailableUrl);

        // Perform GET
        HttpWebResponse response = 
           (HttpWebResponse) request.GetResponse();
        if (HttpStatusCode.OK == response.StatusCode)
        {
            // HTTP = 200, close the request and return true
            response.Close();
            e.Result = true;
        }
        else
        {
            // Other status; return false
            e.Result = false;
        }
    }
    catch (WebException)
    {
        // Deffinitely offline
        e.Result = false;
    }
}</pre> 

　　当BackgroundWorker 对象完成了他的工作，，也就是定义DoWork 里的事件，他会触发RunWorkerCompleted 事件，这个事件也定义了一个定制的事件句柄- RunWorkerCompletedEventArgs 有了这个类，我们就可以管理ToolStripStatusLabel的显示了

<pre class="lang:c# decode:true " >private void BackgroundWorker_RunWorkerCompleted(object 
                   sender, RunWorkerCompletedEventArgs e)
{
    if (null != e.Error)
    {
        // Throw the error to the User Interface...
        // This shouldn't really get to happen.
        Trace.TraceError(e.Error.Message);
        throw e.Error;
    }
    else
    {
        if ((bool) e.Result) // Online
        {
            this.Image = Properties.Resources.Online;
            this.Text = Properties.Resources.MainFormConnectionStatusOnline;
        }
        else // Offline
        {
            this.Image = Properties.Resources.Offline;
            this.Text = Properties.Resources.MainFormConnectionStatusOffline;
        }

        // Redraw
        this.Invalidate();
    }

    // Re-enable and restart timer...
    this.Enabled = true;
    this._updateTimer.Enabled = true;
}</pre> 

### 结论

　　.net 是的使用后台线程很容易，也是的我们的UI有了更加平滑，有好的体验，现在，VS2005强烈建议手工为DoWork指定你的代理 ，因为VS.net有一个bug，当你尝试着重写而不是在另一个独立的线程里使用UI代码，VS可能会替换你写的代码。。
如果你需要使用Web Service，也许你可以考虑添加一个GetVersion的方法到你的Web Service里。然后尝试去访问这个服务。来确定你的web服务器是不是正常可用。

### 历史

　　Demo  version - 1.0.0.0 –随文附
<v>许可</strong>
　　本文没有显式的许可，但是可能包含了一些使用上的条款，不论是在文章里还是在下载的代码里。如果有疑问联系我吧。。

原文地址：[Display an Internet Connection status icon using .NET Framework 2.0](http://www.codeproject.com/Articles/11045/Display-an-Internet-Connection-status-icon-using-N)

著作权声明：本文由http://leaver.me 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！

源代码下载：[InternetConnectionStatus](http://pan.baidu.com/share/link?shareid=61578&uk=1493685990)