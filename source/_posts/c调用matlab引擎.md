title: "C#调用Matlab引擎"
tags:
  - C＃
  - 大学
  - 工作
  - 编程
id: 1340
categories:
  - 学习笔记
date: 2012-08-09 22:39:57
---

　　最近在使用matlab的时候。求多元函数取得最小值的时候。变量的值。于是就用到了sym符号。结果在C#中调用时总是出错。后来Google了N久发现是符号工具箱是无法编译的。。后来找到了替代方法。就是调用matlab 引擎。当然要稍微麻烦一点。因为调用引擎这种方法参数传递比较麻烦。我只想到了通过C#把需要计算的值保存到文本中。然后matlab中load进来。计算完成后 save出去。。

　　调用matlab引擎类似于在C#中新开了一个进程。然后执行一些命令。之前需要引用Matlab Application Type Library。这是个com组件

[![]({{BASE_PATH}}/images/b938768405ede59db17f36bdc27e700bf3cbf1dc.jpg "type")](http://leaverimage.b0.upaiyun.com/25657_o.jpg)
然后在程序里使用 using语句 

<pre class="lang:c# decode:true " >using MLApp;</pre> 
然后就可以正常使用了。使用我试过的有两种方法

<pre class="lang:default decode:true " >MLApp.MLAppClass matlab = new MLApp.MLAppClass();  
matlab.Visible = 1;  
string command="figure(1)";
matlab.Execute(command);  
matlab.Quit();</pre> 

很简单。Visible设置可见性。。command表示要执行的命令。

另一种是

<pre class="lang:c# decode:true " >MLApp.DIMLApp matlab = null;  
Type matlabAppType = System.Type.GetTypeFromProgID("Matlab.Application");  
matlab = System.Activator.CreateInstance(matlabAppType) as MLApp.DIMLApp;  
matlab.Visible = 1;  
string command="figure(2)";
matlab.Execute(command);  
matlab.Quit();</pre> 

不知什么原因，第一种方法我没有调用成功。第二种调用成功。欢迎讨论。