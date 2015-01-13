title: "C#编写文件搜索器"
tags:
  - C＃
  - WPF
  - 大学
  - 学习
  - 翻译
id: 1799
categories:
  - 学习笔记
date: 2012-10-01 11:36:29
---

[![]({{BASE_PATH}}/images/6d2217ea2873cc4f559cc605fc38f29dade4aef8.jpg "filesearcher")](http://leaverimage.b0.upaiyun.com/27654_o.jpg)

**介绍**
在装有Vista的机器上。我想通过一个给定的字符串来搜索我硬盘上的一个文件，该文件内容包含这个字符串序列，资源管理器是做不到的。因此，我就决定自己写吧。然后就写成这样了。。

**我做了什么**

你必须输入一个选择一个搜索目录，这样程序才知道在哪搜索文件/目录，如果你选上了“包含子目录”复选框，程序就会递归地搜索指定目录的子目录，指定的文件名可以是像 "*.wav;*.mp3;Christma??ree.*" 这样的字符串，程序将会列出所有的文件/目录匹配这些文件名

你也可以使用一些限制条件来限制找到的项目，每一个限制条件可以通过选上复选框来激活，限制条件的参数可以在右边选中就行了。

1\. “Files newer than”将会列出LastWriteTime（上次修改时间）晚于指定时间的文件
2\. “Files newer than”将会列出LastWriteTime（上次修改时间）早于指定时间的文件
3\. "Files containing the string"仅仅列出包含字符串参数的文件。

程序将会把字符串转换成字节序列，可以使用ASCII或者Unicode编码，取决于你的选择，然后搜索每一个出现这个字节序列的文件。

点击Start（开始）按钮就开始搜索了。找到的项目会列在下面，如果搜索时间太长了。你可以点击Stop（停止）来停止搜索。

如果你双击下面的一个文件。不是文件夹哦，程序将会根据关联程序打开该文件
如果你邮件一个项目，然后选择“Open Containing Folder”（打开包含文件夹）将会在资源管理器里打开包含该项目的文件夹

如果你想要把搜索结果保存到一个文本文件。输入个分隔符分隔项目，然后点击“Write results to text file…”（保存结果到文本…）

**使用代码**
1\. MainWindow处理所有的界面事务
2\. Searcher类提供了业务逻辑，用来搜索FileSystemInfo对象

当用户点击Start（开始）按钮，Searcher.Start 方法就会执行，该方法开启了一个名为SearchThread 的新线程，这个线程搜索文件/目录，匹配用户输入的参数，如果找到了一个匹配的FileSystemInfo对象，它就出发一个异步的FoundInfo 事件，然后MainWindow就可以从FoundInfoEventArgs中解出FileSystemInfo对象，然后更新结果列表，当线程结束的时候，将m_thread成员对象设置为null，每一次Searcher.Start 执行的时候都会检测m_thread是否为null，因此同时不会有两个线程在运行。

当用户点击Stop(停止)按钮的时候Searcher.Stop 方法被执行，然后设置m_stop 成员为true， Searchthread会注意到这个改变。注意本操作是线程安全的。因为布尔变量只需要一步就操作完成了

重要：在Searcher_FoundInfo 事件处理中，MainWindow使用Invoke方法通过代理来调用this_FoundInfo 方法。通过这个方法，MainWindow是的更新结果列表的代码在MainWindow的线程里执行，而不是在Searcher的线程里，直接调用this_FoundInfo 方法会引发程序崩溃，因为Searcher_FoundInfo 事件处理和图形界面控件不同步。

<pre class="lang:c# decode:true " >private delegate void FoundInfoSyncHandler(FoundInfoEventArgs e);
private FoundInfoSyncHandler FoundInfo;

...

private void MainWindow_Load(object sender, EventArgs e)
{
...
this.FoundInfo += new FoundInfoSyncHandler(this_FoundInfo);
...
}

...

private void Searcher_FoundInfo(FoundInfoEventArgs e)
{
if (!m_closing)
{
this.Invoke(FoundInfo, new object[] { e });
}
}

private void this_FoundInfo(FoundInfoEventArgs e)
{
CreateResultsListItem(e.Info);
}</pre> 

CreateResultsListItem 方法创建并添加一个ListViewItem 到结果列表中，然后展示FilesystemInfo 对象包含的数据，FileSystemInfo 可以是FileInfo 或是DirectoryInfo ，取决于Searcher 找到的结果， is操作符可以用来判断对象的类型，如果是FileInfo独享，列表还会以KB为单位显示文件大小

<pre class="lang:default decode:true " >ListViewItem.ListViewSubItem lvsi = new ListViewItem.ListViewSubItem();
if (info is FileInfo)
{
lvsi.Text = GetBytesStringKB(((FileInfo)info).Length);
}
else
{
lvsi.Text = "";
}</pre> 

当Searcher 线程结束的时候，触发ThreadEnded 事件，因此，MainWindow可以注意到搜索结束，Searcher_ThreadEnded 事件处理句柄使用和Searcher_FoundInfo一样的方式调用Invoke方法。

<pre class="lang:default decode:true " >private delegate void ThreadEndedSyncHandler(ThreadEndedEventArgs e);
private ThreadEndedSyncHandler ThreadEnded;

...

private void MainWindow_Load(object sender, EventArgs e)
{
...
this.ThreadEnded += new ThreadEndedSyncHandler(this_ThreadEnded);
...
}

...

private void Searcher_ThreadEnded(ThreadEndedEventArgs e)
{
if (!m_closing)
{
this.Invoke(ThreadEnded, new object[] { e });
}
}

private void this_ThreadEnded(ThreadEndedEventArgs e)
{
EnableButtons();
if (!e.Success)
{
MessageBox.Show(e.ErrorMsg,
"Error",
MessageBoxButtons.OK,
MessageBoxIcon.Exclamation);
}
}</pre> 

Demo下载
[FileSearcher](http://pan.baidu.com/share/link?shareid=68747&uk=1493685990)
许可
本文包括源代码和文件在CPOL下授权

原文地址：[File-Searcher-in-C](http://www.codeproject.com/Articles/35044/File-Searcher-in-C)

著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！