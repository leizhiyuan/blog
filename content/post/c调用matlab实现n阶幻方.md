---
title: "C#调用MatLab实现N阶幻方"
tags:
  - C＃
  - matlab
  - 大学
  - 总结
  - 编程
  - 软件
id: 1160
categories:
  - 学习笔记
date: 2012-07-02 18:39:43
---

　　MatLab的计算能力太强大了。最近需要通过C#来调用MatLab来进行一些计算，可是MatLab没用过。安装文件在我硬盘里躺了整整一年。

　　我们希望的是由外部程序调用MatLab函数。所以。希望可以完全脱离MATLAB环境，实现软件的快速开发。为此需要先介绍一下MCR。

### MCR简介

　　 MCR的全称是MATLAB Compiler Runtime，即MATLAB编译器运行时。是一个由MATLAB共享类库构成的执行引擎，他能够使MATLAB文件在没有MATLAB的机器上运行。这一点和.NET Framework相对于.NET程序一样，即为程序的运行提供了底层支持。当发布程序的时候，需要将MCR也打包进来，这样没有MATLAB的机器上也能执行，MCR随MATLAB软件一同发布，可以在MATLAB中输入命令“mcr”或者“mcrinstaller”获取其保存路径：

<pre class="lang:default decode:true " >&gt;&gt; mcr
The WIN32 MCR Installer, version 7.15, is:
    D:\Program Files\MATLAB\R2011a\toolbox\compiler\deploy\win32\MCRInstaller.exe

MCR installers for other platforms are located in:
    D:\Program Files\MATLAB\R2011a\toolbox\compiler\deploy\&lt;ARCH&gt;
  &lt;ARCH&gt; is the value of COMPUTER('arch') on the target machine.

Full list of available MCR installers:
D:\Program Files\MATLAB\R2011a\toolbox\compiler\deploy\win32\MCRInstaller.exe

For more information, read your local MCR Installer help.
Or see the online documentation at MathWorks' web site. (Page may load slowly.)</pre> 

　　根据上面的运行结果，可知该文件的完整路径是：

<pre class="lang:default decode:true " >D:\Program Files\MATLAB\R2011a\toolbox\compiler\deploy\win32\MCRInstaller.exe</pre> 

### MWArray API简介

 　MCR包含了文件MWArray.dll，该文件中的API承担了用户程序和MCR之间数据交换的任务，因此，每一个独立文件都需要包含对该文件的引用，否则程序就不能使用MATLAB中的函数，为使用该文件，需要先使用上一步找到的安装MCR，我安装在D:\Program Files\MATLAB\MATLAB Compiler Runtime目录，然后该dll该文件在安装完MCR后位于：

<pre class="lang:default decode:true " >D:\Program Files\MATLAB\MATLAB Compiler Runtime\v715\toolbox\dotnetbuilder\bin\win32\v2.0，名字是MWArray.dll，</pre> 

　　另外，该文件中有两个重要的命名空间，MathWorks.MATLAB.NET.Arrays和MathWorks.MATLAB.NET.Utility，Arrays命名空间下的类提供从其他任何兼容CLS（Common Language Specification） 语言访问MATLAB中数组的功能，这些类支持数组格式化、类型的特定索引和错误处理的功能。而Utility命名空间下的类提供了对MWArray类架构和MATLAB公共运行时的托管API的通用支持。

### 实施

> 为了将MATLAB中的程序作为组件提供给其他.NET程序使用，需要做两方面的工作：
> 
> 1.将M文件打包为与.NET兼容的程序集
> 
> 2.在外部程序中添加对程序集的引用
> 
> ### MATLAB端的工作
> 
> 　　以我这个功能为例。打开MatLab，然后File->New->Deployment Project.然后在Type里选择.net Assembly,Ok即可。我选择的工程名是test，注意，这个test就会生成一个名为test.dll的程序集，所以起个好名字，，我没起好。如图1
> [![](/images/494daca0321a65539ea6b2c7d6d3155345c3c32d.jpg "start")](http://leaverimage.b0.upaiyun.com/23964_o.jpg)
> 　　然后在图2的视图中定位到你选择的工程目录，并依次点击。
> [![](/images/b2e74076bfe76e6aeac6b3b7365668d2f23431fe.jpg "step")](http://leaverimage.b0.upaiyun.com/23966_o.jpg)
> 　　添加一个test.m的文件。图3
> [![](/images/110d97e0942789e04445dd3d3eee89873c49de11.jpg "test")](http://leaverimage.b0.upaiyun.com/23965_o.jpg)这个名称随意。但是要注意，这个名字必须和你里面将要写的那个函数名称一致，这个很好理解。好了以后，双击test.m就会打开。输入
> 
> <pre class="lang:default decode:true " >function m=test(n)
> m=magic(n);
> end</pre> 
> 
> 　　创建一个test方法。该方法在C#调用的时候要用到，接受一个参数，然后输出一个幻方。保存关闭m文件，然后回到上图，双击test.prj也就是工程文件。
> 来到图4
> [![](/images/5ab49b4504bc1aba33d62b3c2cfbf897656c751d.jpg "add")](http://leaverimage.b0.upaiyun.com/23967_o.jpg)
> 　　点击Add Class，就是向test.dll的程序集中添加类。我添加了一个类叫做Func1.这个类用来在C#中创建一个对象，然后调用test（n）这个函数，然后出现Add Files，点击打开浏览框，把刚才写好的test.m选上。就可以了
> 　　最后看窗口右上角，，点击那个build图标就ok，等个几分钟，就会在目录里生成test.dll文件了。图5
> [![](/images/4e21f5823ece422097e73e2321e43b3b09a2cb7a.jpg "build")](http://leaverimage.b0.upaiyun.com/23968_o.jpg)
> 
> ### VS端的代码
> 
> 　　现在，打开vs，创建C#工程，添加两个引用，分别是MWArray.dll和test.dll，添加引用的方法就是在解决方案资源管理器中，的引用上，点击，然后添加引用->浏览->找到这两个dll，为了方便，我一般会把这两个dll复制到我的C#工程目录里，然后添加引用，如前面所说，MWArray.dll里有两个命名空间，我们使用using语句都添加上。也包括生成的test.dll
> 
> <pre class="lang:c# decode:true " >using test;
> using MathWorks.MATLAB.NET.Arrays;
> using MathWorks.MATLAB.NET.Utility;</pre> 
> 
> 　　然后设计C#界面，很简单的拖一个界面，图6
> [![](/images/07a97d0fe021665574597469b813e53dc6b48677.jpg "ui")](http://leaverimage.b0.upaiyun.com/23969_o.jpg)
> 　　然后单击事件代码：
> 
> <pre class="lang:c# decode:true " >            Func1 f = new Func1(); 
>             MWNumericArray na = null;
>             MWArray[] ansArray = null; 
>             int i = Convert.ToInt16(textBox1.Text); 
>             ansArray = f.test(1, i); 
>             na = (MWNumericArray)ansArray[0];//只有一个数组返回
>             MessageBox.Show(na.ToString());</pre> 
> 　　看到test有两个参数，第一个参数是指返回结果书，我们只需要一个就好了。所以为1，后面那个是阶数了。运行结果如图7：
> [![](/images/891d04e30b4742ae3ca5b851d5fc379cc3d10950.jpg "result")](http://leaverimage.b0.upaiyun.com/23970_o.jpg)

　　注意程序在初始化自定义类Func1时会花费较多时间，大约持续2~3秒钟，程序如同假死一般，但第二次点击按钮反应却很快.
参考：
    [http://www.360doc.com/content/12/0530/10/9587654_214689438.shtml](http://www.360doc.com/content/12/0530/10/9587654_214689438.shtml)