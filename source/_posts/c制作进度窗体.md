title: "C#制作进度窗体"
tags:
  - 大学
  - 开发
  - 编程
  - 翻译
  - 设计
  - 软件
id: 1935
categories:
  - 我的翻译
date: 2012-10-10 09:07:41
---

**介绍**

这是我在CodeProject上的第一篇文章。我希望对你有用

当我开发软件的时候。我通常因为一个很耗时是任务需要完成。而请求让用户等待，并且通过也允许用户取消。不论我做何种操作（比如下载文件。保存大文件等等）。我都需要做下面几件事：

*   通过一个模态对话框来让用户等待操作完成
*   能让用户看到进度。
*   能让用户随时取消。
我搜了好久也没找到拿来就能用的窗体控件，也许是我没找到。于是我自己写。。
图1

<<<<<<< HEAD
![]({{BASE_PATH}}/images/9f46aad8d865255936532f68d592d8ffd45b8b3d.jpg "progress")
=======
![](/images/9f46aad8d865255936532f68d592d8ffd45b8b3d.jpg "progress")
>>>>>>> 换电脑之后重新备份

**背景**

BackgroundWorker 类包含了我需要完成任务的所有东西。我只需要给他提供一个对话框。

**使用代码**

ProgressForm 包含了一个BackgroundWorker ，你要做的仅仅就是提供了一个完成工作的方法。
<pre class="lang:default decode:true">ProgressForm form = new ProgressForm();
form.DoWork += new ProgressForm.DoWorkEventHandler(form_DoWork);
//如果想为后台任务提供参数的话
form.Argument = something;</pre>
为了开始BackgroundWorker，只需要调用ShowDialog 方法。返回值则取决于任务是怎么完成的。
<pre class="lang:default decode:true">DialogResult result = form.ShowDialog();
if (result == DialogResult.Cancel)
{
//用户点击了取消
}
else if (result == DialogResult.Abort)
{
/未处理的异常抛出
//你可以得到异常信息
MessageBox.Show(form.Result.Error.Message);
}
else if (result == DialogResult.OK)
{
//正常完成
//结果存储在 form.Result里
}</pre>
&nbsp;

最后。任务方法看起来是这样的。
<pre class="lang:default decode:true">void form_DoWork(ProgressForm sender, DoWorkEventArgs e)
{
//得到参数
object myArgument = e.Argument;

//做一些耗时的任务...
for (int i = 0; i &lt; 100; i++)
{
//通知进度
sender.SetProgress(i, "Step " + i.ToString() + " / 100...");

//...

//检查是否点击了取消
if (sender.CancellationPending)
{
e.Cancel = true;
return;
}
}
}</pre>
如果你想要改改进度条，或者进度条显示的文本。SetProgress 有一些重载的方法
<pre class="lang:default decode:true">public void SetProgress(string status);
public void SetProgress(int percent);
public void SetProgress(int percent, string status);</pre>
最后一个可自定义的字符串是：有两个预定义的字符串CancellingText 和DefaultStatusText. CancellingText ，这两个字符串，当用户点击取消的时候显示

**如何实现**
ProgressForm 紧紧嵌入了一个BackgroundWorker ，并包装进了主函数。

首先。我设计了如图所示的一个窗体，然后。添加了BackgroundWorker。
<pre class="lang:default decode:true">public partial class ProgressForm : Form
{
public ProgressForm()
{
InitializeComponent();

worker = new BackgroundWorker();
worker.WorkerReportsProgress = true;
worker.WorkerSupportsCancellation = true;
worker.DoWork += new System.ComponentModel.DoWorkEventHandler(worker_DoWork);
worker.ProgressChanged += new ProgressChangedEventHandler(
worker_ProgressChanged);
worker.RunWorkerCompleted += new RunWorkerCompletedEventHandler(
worker_RunWorkerCompleted);
}
void worker_DoWork(object sender, DoWorkEventArgs e)
{
}
void worker_ProgressChanged(object sender, ProgressChangedEventArgs e)
{
}
void worker_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
}
BackgroundWorker worker;
}</pre>
我们必须把DoWork事件暴露给用户。我添加了一个委托。这样。我可以很容易的访问窗体成员
<pre class="lang:default decode:true">public delegate void DoWorkEventHandler(ProgressForm sender, DoWorkEventArgs e);
public event DoWorkEventHandler DoWork;

void worker_DoWork(object sender, DoWorkEventArgs e)
{
//后台任务开始
//调用用户的事件处理程序
if (DoWork != null)
DoWork(this, e);
}</pre>
&nbsp;

好。我们已经有了任务和事件。先爱。我们希望当窗体显示的时候。后台任务尽可能开始。我们在Load事件中写代码
<pre class="lang:default decode:true">void ProgressForm_Load(object sender, EventArgs e)
{
worker.RunWorkerAsync();
}</pre>
现在写一个方法通知进度。添加代码到ProgressChanged 事件处理程序中
<pre class="lang:default decode:true">public void SetProgress(int percent, string status)
{
worker.ReportProgress(percent, status);
}
void worker_ProgressChanged(object sender, ProgressChangedEventArgs e)
{
if (e.ProgressPercentage &gt;= progressBar.Minimum &amp;&amp;
e.ProgressPercentage &lt;= progressBar.Maximum)
{
progressBar.Value = e.ProgressPercentage;
}
if (e.UserState != null)
labelStatus.Text = e.UserState.ToString();
}</pre>
我们快做好了。现在我们添加取消按钮
<pre class="lang:default decode:true">void buttonCancel_Click(object sender, EventArgs e)
{
//通过worker我们要取消
worker.CancelAsync();
//使取消按钮不可用，改变状态文本
buttonCancel.Enabled = false;
labelStatus.Text = "Cancelling..."
}</pre>
最后一件事是我们想要当worker完成的时候自动关闭窗体，因为我们的worker通过ShowDialog 方法启动。如果直接接收返回结果会很好
<pre class="lang:default decode:true">void worker_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
//ShowDialog返回值会指示worker是不是正确完成了
if (e.Error != null)
DialogResult = DialogResult.Abort;
else if (e.Cancelled)
DialogResult = DialogResult.Cancel;
else
DialogResult = DialogResult.OK;
//关闭窗体
Close();
}</pre>
主要的工作就完成了。我添加了一些预定义的字符串啊。如果正在取消。保护状态不会改变。还有传递参数啊。
完整的代码如下：
<pre class="lang:default decode:true">/// &lt;summary&gt;
/// 简单的进度窗体
/// &lt;/summary&gt;
public partial class ProgressForm : Form
{
/// &lt;summary&gt;
/// 获得进度条以对他自定义
/// 在显示窗体之前.
/// 不要在后台任务中直接使用 !
/// &lt;/summary&gt;
public ProgressBar ProgressBar { get { return progressBar; } }
/// &lt;summary&gt;
/// 传递给后台任务的参数.
/// &lt;/summary&gt;
public object Argument { get; set; }
/// &lt;summary&gt;
/// 后台任务的结果.
///也可以检查ShowDialog返回值
///来看看是不是正确完成了.
/// &lt;/summary&gt;

public RunWorkerCompletedEventArgs Result { get; private set; }
/// &lt;summary&gt;
/// 如果点击了取消按钮则为true
///后台任务还在执行
/// &lt;/summary&gt;
public bool CancellationPending
{
get { return worker.CancellationPending; }
}

/// &lt;summary&gt;
/// 取消按钮被点击之后的显示文本
/// &lt;/summary&gt;
public string CancellingText { get; set; }
/// &lt;summary&gt;
/// 缺省状态文本.
/// &lt;/summary&gt;
public string DefaultStatusText { get; set; }
/// &lt;summary&gt;
/// DoWork事件的委托.
/// &lt;/summary&gt;
/// &lt;param name="sender"&gt;T事件源.&lt;/param&gt;
/// &lt;param name="e"&gt;包含事件数据.&lt;/param&gt;
public delegate void DoWorkEventHandler(ProgressForm sender, DoWorkEventArgs e);
/// &lt;summary&gt;
/// 当后台任务开始的时候发生.
/// &lt;/summary&gt;
public event DoWorkEventHandler DoWork;

/// &lt;summary&gt;
/// 构造函数.
/// &lt;/summary&gt;
public ProgressForm()
{
InitializeComponent();

DefaultStatusText = "Please wait...";
CancellingText = "Cancelling operation...";

worker = new BackgroundWorker();
worker.WorkerReportsProgress = true;
worker.WorkerSupportsCancellation = true;
worker.DoWork += new System.ComponentModel.DoWorkEventHandler(worker_DoWork);
worker.ProgressChanged += new ProgressChangedEventHandler(worker_ProgressChanged);
worker.RunWorkerCompleted += new RunWorkerCompletedEventHandler(
worker_RunWorkerCompleted);
}

/// &lt;summary&gt;
/// 改变状态文本.
/// &lt;/summary&gt;
/// &lt;param name="status"&gt;新状态文本.&lt;/param&gt;
public void SetProgress(string status)
{
//如果没有改变
//或者取消请求还在处理就不改变状态文本
if (status != lastStatus &amp;&amp; !worker.CancellationPending)
{
lastStatus = status;
worker.ReportProgress(progressBar.Minimum - 1, status);
}
}
/// &lt;summary&gt;
///改变进度条的值
/// &lt;/summary&gt;
/// &lt;param name="percent"&gt;新值.&lt;/param&gt;
public void SetProgress(int percent)
{
//如果值没有改变就不要更新进度条
if (percent != lastPercent)
{
lastPercent = percent;
worker.ReportProgress(percent);
}
}
/// &lt;summary&gt;
///改变进度条值和文本.
/// &lt;/summary&gt;
/// &lt;param name="percent"&gt;N新值.&lt;/param&gt;
/// &lt;param name="status"&gt;新文本.&lt;/param&gt;
public void SetProgress(int percent, string status)
{
//如果至少一个改变就调用
if (percent != lastPercent || (status != lastStatus &amp;&amp; !worker.CancellationPending))
{
lastPercent = percent;
lastStatus = status;
worker.ReportProgress(percent, status);
}
}

private void ProgressForm_Load(object sender, EventArgs e)
{
//重用窗体，恢复缺省值
Result = null;
buttonCancel.Enabled = true;
progressBar.Value = progressBar.Minimum;
labelStatus.Text = DefaultStatusText;
lastStatus = DefaultStatusText;
lastPercent = progressBar.Minimum;
//窗体已载入就开始后台任务
worker.RunWorkerAsync(Argument);
}

private void buttonCancel_Click(object sender, EventArgs e)
{
//通知后台任务，我们要取消
worker.CancelAsync();
//取消按钮不可用，改变文本
buttonCancel.Enabled = false;
labelStatus.Text = CancellingText;
}

void worker_DoWork(object sender, DoWorkEventArgs e)
{
//后台任务开始
//调用用户的处理程序
if (DoWork != null)
DoWork(this, e);
}

void worker_ProgressChanged(object sender, ProgressChangedEventArgs e)
{
//确保新值可用并更新
if (e.ProgressPercentage &gt;= progressBar.Minimum &amp;&amp;
e.ProgressPercentage &lt;= progressBar.Maximum)
{
progressBar.Value = e.ProgressPercentage;
}
//如果取消请求正在处理就不要更新
if (e.UserState != null &amp;&amp; !worker.CancellationPending)
labelStatus.Text = e.UserState.ToString();
}

void worker_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
{
//后台任务完成
//保持结果，关闭窗体
Result = e;
if (e.Error != null)
DialogResult = DialogResult.Abort;
else if (e.Cancelled)
DialogResult = DialogResult.Cancel;
else
DialogResult = DialogResult.OK;
Close();
}

BackgroundWorker worker;
int lastPercent;
string lastStatus;
}</pre>
**结论**
窗体简单，我通常用。希望对你们也有用

**Demo下载**

[源码和测试下载](http://pan.baidu.com/share/link?shareid=79426&amp;uk=1493685990)

**许可**
本文，包括源代码和文件在CPOL下授权。

&nbsp;

原文地址：[ProgressForm-A-simple-form-linked-to-a-BackgroundW](http://www.codeproject.com/Articles/160219/ProgressForm-A-simple-form-linked-to-a-BackgroundW)
著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！