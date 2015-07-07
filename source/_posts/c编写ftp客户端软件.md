title: "C#编写FTP客户端软件"
tags:
  - C＃
  - windows
  - 大学
  - 学习
  - 编程
  - 翻译
  - 软件
id: 1928
categories:
  - 我的翻译
date: 2012-10-09 09:06:16
---

# [![](/images/df5f8fbfeaeabe639c893a078ddeda6cac4361db.png "1")](http://leaverimage.b0.upaiyun.com/27933_o.png)

# 1 介绍

我知道。网上有很多现成的FTP软件。但是。我们也想要了解FTP的一些底层机构，因此。 这个开源的项目在你学习FTP知识的时候也许对你有些帮组。程序的界面看起来像FileZilla，FileZilla虽然流行但是有些bug，当我打开我博客的时候总是有问题。我需要通过FTP连接我的服务器。发送文件，下载文件等等。因为。我决定写我自己的软件来处理所有的情况。FileZilla足够好。但它不是我的。

&nbsp;

# 2 背景

&nbsp;

看看我们已经知道的。我们知道FTP是一个标准的基于TCP网络协议。用于从一个主机向另一个主机传输文件。它是一个C/S架构。

&nbsp;

图2

[![](/images/ceb7abd419bd3e983fa8f9cbf1962546cc0880a4.gif "2")](http://leaverimage.b0.upaiyun.com/27934_o.gif)

&nbsp;

FTP程序曾经是基于命令行的。我们仍沿可以通过cmd.exe连接FTP服务器。因为FTP的确可以通过命令来操作。举个例子。我们可以在命令行使用“stor”命令来发送文件。为了完成这些请求。FTP服务器需要一直运行等待即将到来的客户端请求。我们可以从来自维基百科的解释更好的理解FTP：

&nbsp;

客户端计算机可以通过服务器的21端口和服务器通信。叫做控制连接。它在一次会话期间保持开放。第一次连接的时候。叫做数据连接,服务器可以对客户端打开20端口（主动模式），建立一条数据通路，连接上客户端传输数据。或者客户端打开一个随机的端口（被动模式），去连接服务器，来传输数据。控制连接使用一个类似Telnet的协议，被用作客户端和服务器会话管理（命令，标识，密码）。。比如。"RETR _filename_"  会从服务器端下载文件。

图三

![](/images/9c06cc0d8d904390202ef269a18467324df590c9.gif "3")

一个完整的FTP文件传输需要建立两种类型的连接，一种为文件传输下命令，称为控制连接，另一种实现真正的文件传输，称为数据连接。

&nbsp;

&nbsp;

服务器 通过三位ASCII的数字状态码，可能包含可选的描述信息，在控制连接上做出回应。比如。“200”或者是“200 OK”,表示上一条命令成功了。数字代表编号，描述信息给出了一些说明（比如“OK”）,或者可能是一些需要的参数(比如需要帐号来存储文件)，那么我们需要怎么做呢。很明显。发送命令，接收“OK”回应，发送数据。接收数据。完了。但是首先需要服务器已经准备好了。FTP服务器可以在主动和被动两种模式下运行。主动模式是基于服务器的连接而被动模式是基友客户端的连接。继续看。

&nbsp;

&nbsp;

&nbsp;

在主动连接中，客户端把自己的ip和端口发送给服务器。然后服务器尝试连接到客户端，但是可能会因为防火墙的原因而被拒绝。我们在windows上都会使用反病毒/自带防火墙。是吧。那么我们来看看被动模式

&nbsp;

在被动连接中。服务器通过一个“PASV”命令把自己的ip和端口发送给客户端。然后客户端通过该IP尝试连接服务器。对于发送文件非常有用。当我们发送文件的时候。优先使用“PASV”模式，如你们所说。大多数协议。像FTP/HTTP 使用ASCII编码，因为全球可用。因此我们会使用这种编码。你可以从下面得到FTP的命令列表

&nbsp;

主动和被动都是对于服务器端来说的

# 3 使用代码

现在我们已经为编写软件做好准备了。我们写些有用的代码吧。：）首先。我们“打开文件对话框”，集成到我们的窗体里。

&nbsp;

## 3.1 资源管理器组件

&nbsp;

我们需要一个资源管理器组件在软件界面可以看到我们所有的文件。这样我们才可以选择哪些文件来发送到FTP服务器，新建一个Windows窗体控件库（下载包中提供了）

&nbsp;

图四

[![](/images/b2ae5f59d51b1a183a0ceea50a2c9497971422e7.png "4")](http://leaverimage.b0.upaiyun.com/27937_o.png)

&nbsp;

最后看起来样子是上面这样。先添加一个TreeView，一些按钮，和一个搜索功能
<div>
<pre class="lang:default decode:true ">TreeView.Nodes.Clear();

TreeNode nodeD = new TreeNode();

nodeD.Tag = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);

nodeD.Text = "Desktop";

nodeD.ImageIndex = 10;

nodeD.SelectedImageIndex = 10;

TreeView.Nodes.Add(nodeD);</pre>
&nbsp;

</div>
就像上面代码展示的那样。我们需要添加地一个主节点。我的文档。我的电脑等等。然后获得子目录。

&nbsp;

&nbsp;
<pre class="lang:default decode:true">string[] dirList;
dirList = Directory.GetDirectories(parentNode.Tag.ToString());
Array.Sort(dirList);
if (dirList.Length == parentNode.Nodes.Count)
    return;
for (int i = 0; i &lt; dirList.Length; i++)
{
    node = new TreeNode();
    node.Tag = dirList[i]; 
    node.Text = dirList[i].Substring(dirList[i].LastIndexOf(@"\") + 1);
    node.ImageIndex = 1;
    parentNode.Nodes.Add(node);
}</pre>
&nbsp;

可以从下载包里看到完整的代码。我们还应该处理鼠标单击事件。

&nbsp;

现在我们有了一个资源管理器。还有FTP和VS所需要的所有信息。

首先，我们连接服务器。我们应该怎么做呢？
<pre class="lang:default decode:true">FTPSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
AppendText(rchLog,"Status : Resolving IP Address\n",Color.Red);
remoteAddress = Dns.GetHostEntry(Server).AddressList[0];
AppendText(rchLog, "Status : IP Address Found -&gt;" + remoteAddress.ToString() + "\n", Color.Red);
addrEndPoint = new IPEndPoint(remoteAddress, Port);
AppendText(rchLog,"Status : EndPoint Found -&gt;" + addrEndPoint.ToString() + "\n", Color.Red);
FTPSocket.Connect(addrEndPoint);
是的。我们需要一个socket连接到服务器 ，然后发送命令

AppendText(rchLog, "Command : " + msg + "\n", Color.Blue);
Byte[] CommandBytes = Encoding.ASCII.GetBytes((msg + "\r\n").ToCharArray());
FTPSocket.Send(CommandBytes, CommandBytes.Length, 0);
//read Response
ReadResponse();</pre>
&nbsp;

&nbsp;

我们发送命令到服务器。服务器以他自己的语言来响应。我们需要理解他。响应包括3位数字和一些解释。

&nbsp;
<pre class="lang:default decode:true">private string SplitResponse()
{
    try
    {
        while (true)
        {
            Bytes = FTPSocket.Receive(Buffer, Buffer.Length, 0); //Number Of Bytes (Count)
            StatusMessage += Encoding.ASCII.GetString(Buffer, 0, Bytes); //Convert to String
            if (Bytes &lt; Buffer.Length)  //End Of Response
                break;
        }
        string[] msg = StatusMessage.Split('\n');
        if (StatusMessage.Length &gt; 2)
            StatusMessage = msg[msg.Length - 2];  //Remove Last \n
        else
            StatusMessage = msg[0];
        if (!StatusMessage.Substring(3, 1).Equals(" "))
            return SplitResponse();
        for (int i = 0; i &lt; msg.Length - 1; i++)
            AppendText(rchLog, "Response : " + msg[i] + "\n", Color.Green);
        return StatusMessage;
    }
    catch(Exception ex)
    {
        AppendText(rchLog, "Status : ERROR. " +ex.Message+ "\n", Color.Red);
        FTPSocket.Close();
        return "";
    }
}</pre>
&nbsp;

完了。现在我们可以下载，上传，重命名。或者删除了。

&nbsp;

&nbsp;

# 4 FTP命令列表

<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top">命令</td>
<td valign="top">描述</td>
</tr>
<tr>
<td valign="top">ABOR</td>
<td valign="top">中断一个活动的文件传输</td>
</tr>
<tr>
<td valign="top">ACCT</td>
<td valign="top">系统帐号信息</td>
</tr>
<tr>
<td valign="top">ADAT</td>
<td valign="top">认证/安全数据</td>
</tr>
<tr>
<td valign="top">ALLO</td>
<td valign="top">分配空间来接收文件</td>
</tr>
<tr>
<td valign="top">APPE</td>
<td valign="top">添加文件到服务器</td>
</tr>
<tr>
<td valign="top">AUTH</td>
<td valign="top">认证/安全机制</td>
</tr>
<tr>
<td valign="top">CCC</td>
<td valign="top">清除命令通道</td>
</tr>
<tr>
<td valign="top">CDUP</td>
<td valign="top">转到父目录</td>
</tr>
<tr>
<td valign="top">CONF</td>
<td valign="top">机密性保护命令</td>
</tr>
<tr>
<td valign="top">CWD</td>
<td valign="top">改变服务器上的工作目录</td>
</tr>
<tr>
<td valign="top">DELE</td>
<td valign="top">删除服务器上的指定文件</td>
</tr>
<tr>
<td valign="top">ENC</td>
<td valign="top">机密保护通道</td>
</tr>
<tr>
<td valign="top">EPRT</td>
<td valign="top">指定一个服务器应该连接的扩展地址和端口</td>
</tr>
<tr>
<td valign="top">EPSV</td>
<td valign="top">进入扩展的被动模式</td>
</tr>
<tr>
<td valign="top">FEAT</td>
<td valign="top">得到服务器提供的功能列表</td>
</tr>
<tr>
<td valign="top">HELP</td>
<td valign="top">返回指定命令信息</td>
</tr>
<tr>
<td valign="top">LANG</td>
<td valign="top">返回语言信息</td>
</tr>
<tr>
<td valign="top">LIST</td>
<td valign="top">如果是文件名列出文件信息，如果是目录则列出文件列表</td>
</tr>
<tr>
<td valign="top">LPRT</td>
<td valign="top">指定一个服务器应该连接的长地址和端口</td>
</tr>
<tr>
<td valign="top">LPSV</td>
<td valign="top">进入长被动模式</td>
</tr>
<tr>
<td valign="top">MDTM</td>
<td valign="top">返回文件最近修改时间</td>
</tr>
<tr>
<td valign="top">MIC</td>
<td valign="top">完整的保护命令</td>
</tr>
<tr>
<td valign="top">MKD</td>
<td valign="top">创建目录</td>
</tr>
<tr>
<td valign="top">MLSD</td>
<td valign="top">列出指定目录的内容</td>
</tr>
<tr>
<td valign="top">MLST</td>
<td valign="top">在命令行上列出对象的精确信息</td>
</tr>
<tr>
<td valign="top">MODE</td>
<td valign="top">传输模式(S=流模式，B=块模式，C=压缩模式)</td>
</tr>
<tr>
<td valign="top">NLST</td>
<td valign="top">列出指定目录内容</td>
</tr>
<tr>
<td valign="top">NOOP</td>
<td valign="top">无动作，（虚包，来自常连接中）</td>
</tr>
<tr>
<td valign="top">OPTS</td>
<td valign="top">选择一个功能的选项</td>
</tr>
<tr>
<td valign="top">PASS</td>
<td valign="top">验证密码</td>
</tr>
<tr>
<td valign="top">PASV</td>
<td valign="top">进入被动模式</td>
</tr>
<tr>
<td valign="top">PBSZ</td>
<td valign="top">保护缓冲区大小</td>
</tr>
<tr>
<td valign="top">PORT</td>
<td valign="top">指定服务器应该连接的地址和端口</td>
</tr>
<tr>
<td valign="top">PROT</td>
<td valign="top">数据通道保护级别</td>
</tr>
<tr>
<td valign="top">PWD</td>
<td valign="top">显示当前工作目录</td>
</tr>
<tr>
<td valign="top">QUIT</td>
<td valign="top">从FTP服务器上退出登录</td>
</tr>
<tr>
<td valign="top">REIN</td>
<td valign="top">重新初始化登录状态连接</td>
</tr>
<tr>
<td valign="top">REST</td>
<td valign="top">由指定点重启文件传递</td>
</tr>
<tr>
<td valign="top">RETR</td>
<td valign="top">传递文件副本</td>
</tr>
<tr>
<td valign="top">RMD</td>
<td valign="top">在服务器上删除指定目录</td>
</tr>
<tr>
<td valign="top">RNFR</td>
<td valign="top">从旧名称重命名</td>
</tr>
<tr>
<td valign="top">RNTO</td>
<td valign="top">到新名称</td>
</tr>
<tr>
<td valign="top">SITE</td>
<td valign="top">向远程服务器发送指定命令</td>
</tr>
<tr>
<td valign="top">SIZE</td>
<td valign="top">返回文件大小</td>
</tr>
<tr>
<td valign="top">SMNT</td>
<td valign="top">挂载指定文件结构</td>
</tr>
<tr>
<td valign="top">STAT</td>
<td valign="top">返回当前状态</td>
</tr>
<tr>
<td valign="top">STOR</td>
<td valign="top">储存(复制)文件到服务器上</td>
</tr>
<tr>
<td valign="top">STOU</td>
<td valign="top">储存文件到服务器指定文件上</td>
</tr>
<tr>
<td valign="top">STRU</td>
<td valign="top">设置结构(F=文件，R=记录，P=页面)</td>
</tr>
<tr>
<td valign="top">SYST</td>
<td valign="top">返回服务器使用的操作系统</td>
</tr>
<tr>
<td valign="top">TYPE</td>
<td valign="top">传输模式(A=ASCII，E=EBCDIC，I=binary)</td>
</tr>
<tr>
<td valign="top">USER</td>
<td valign="top">验证用户</td>
</tr>
<tr>
<td valign="top">XCUP</td>
<td valign="top">跳到当前工作路径的父目录</td>
</tr>
<tr>
<td valign="top">XMKD</td>
<td valign="top">新建目录</td>
</tr>
<tr>
<td valign="top">XPWD</td>
<td valign="top">输出当前工作目录</td>
</tr>
<tr>
<td valign="top">XRMD</td>
<td valign="top">删除目录</td>
</tr>
</tbody>
</table>
&nbsp;

# 5 返回码列表

*   2xx –返回成功
*   4xx or 5xx – 返回失败
*   1xx or 3xx – 错误或不完整的回复

第二位定义了错误的类型

*   x0z – 语法 – 有语法错误.
*   x1z – 信息 – 请求提供信息
*   x2z – 连接 – 说明控制或数据连接有问题
*   x3z – 账户认证 – 登录或者是账户认证有问题.
*   x4z – 未定义
*   x5z – 文件系统 – 可能是服务器文件系统有问题。
&nbsp;

# Demo 下载

[C#编写FTP客户端](http://pan.baidu.com/share/link?shareid=77343&amp;uk=1493685990)

# 6 许可

本文包括源代码和文件在CPOL下授权。

&nbsp;

原文地址：[File-Transfer-Protocol-FTP-Client](http://www.codeproject.com/Articles/293391/File-Transfer-Protocol-FTP-Client)

著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！