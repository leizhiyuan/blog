title: "远程管理Demo(C#)"
tags:
  - C＃
  - windows
  - 编程
  - 设计
  - 软件
id: 2542
categories:
  - 学习笔记
date: 2013-01-04 15:18:06
---

一个C#的通信的例子

[![]({{BASE_PATH}}/images/8a608877e1ae0aef14d26fea8260f247bbb7ee33.jpg)](http://leaverimage.b0.upaiyun.com/31142_o.jpg)

1.服务端，服务端通过ip和端口生成客户端之后，点击开始监听后，便开启监听线程持续监听，同时注册断开连接和收到信息的事件。收到来自TcpClient 流中的信息后，解析之，如果是连接信息，就添加到连接列表，这样服务端就可以显示多个客户端了。如果是断开信息，就删掉。如果服务端想要给客户端发消息，就选中该客户，然后填写信息，就会调用连接类的发送方法。

&nbsp;

2.客户端，也就是被控端，被控端通过tcp连接到远端ip，然后发送连接成功状态，随后异步读取。读取到信息后调用解析方式。然后处理。。

3.服务端如何生成客户端。其实也比较简单。就是写好客户端以后，保存为文本。然后通过CodeDomProvider的相关方法来编译即可。代码如下：
<pre class="lang:default decode:true"> public static bool Compile(string EXE_Name, string Source)
        {
            CodeDomProvider Compiler = CodeDomProvider.CreateProvider("CSharp");
            CompilerParameters Parameters = new CompilerParameters();
            CompilerResults cResults = default(CompilerResults);

            Parameters.GenerateExecutable = true;
            Parameters.OutputAssembly = EXE_Name;
            Parameters.ReferencedAssemblies.Add("System.dll");
            Parameters.CompilerOptions = " /target:winexe";
            Parameters.TreatWarningsAsErrors = false;

            cResults = Compiler.CompileAssemblyFromSource(Parameters, Source);

            if (cResults.Errors.Count &gt; 0)
            {
                foreach (CompilerError CompilerError_loopVariable in cResults.Errors)
                {
                    CompilerError error = CompilerError_loopVariable;
                    MessageBox.Show("Error: " + error.ErrorText, "", MessageBoxButtons.OK, MessageBoxIcon.Error);
                }
                return false;
            }
            else if (cResults.Errors.Count == 0)
            {
                return true;
            }
            return true;
        }</pre>
源码下载：[CSharp RAT Example.zip](http://pan.baidu.com/share/link?shareid=223714&amp;uk=1493685990)