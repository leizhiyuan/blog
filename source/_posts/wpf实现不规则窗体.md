title: WPF实现不规则窗体
tags:
  - 学习
  - 开发
  - 总结
  - 编程
  - 软件
id: 1008
categories:
  - 学习笔记
date: 2012-06-23 06:22:49
---

这几天在想C# winform程序界面实在太单一，而我[C#实现不规则窗体](http://leaver.me/archives/990.html)中也说了，如果用背景这种东西来做的话，效果很差，抗锯齿能力基本为0，所以我当时在博客园提问，然后园友有了很给力的回答，比如WPF来做，或者第三方插件，或者深入底层改写ONPaint函数的，今天没事，恰好看到了一篇文章讲这个的，于是，就做一个简单的Demo出来，华丽的效果有木有，先看效果图

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/ba031241897a97ce5f76970d0787694511d3960a.jpg "效果")](http://leaverimage.b0.upaiyun.com/23622_o.jpg)
=======
[![](/images/ba031241897a97ce5f76970d0787694511d3960a.jpg "效果")](http://leaverimage.b0.upaiyun.com/23622_o.jpg)
>>>>>>> 换电脑之后重新备份
在win 7下使用win+Tab切换效果也很华丽。就不演示了。

做起来还算比较简单，首先使用Microsoft Expression Design 4 设计一个界面，破解版什么的太多了，，软件界面和ps挺像，不过功能弱很多，自己操作操作就好了，我说一个问题，就是我当时想画一个空心的圆，也就是一个圆环，ps里大家都知道，直接选区相减就可以了，但是这个死活没找到，基本上最后这个界面所有的地方被找了一遍，猜了猜，才发现了，

具体操作如下，首先汇出一个圆形，然后在圆里面再绘出一个圆形，这时候选中第二次的这个小圆，点击屏幕右侧的那个箭头会出现高级选项，

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/7bb961d46945780dd51bf752d4109eb6dfec9583.jpg "高级")](http://leaverimage.b0.upaiyun.com/23623_o.jpg)

然后选择混合模式为橡皮擦，就会擦去这个小圆，于是就只剩下一个圆环了。

[![]({{BASE_PATH}}/images/47a442eae19c0450c9de4ef532d026c4608439f1.jpg "橡皮擦")](http://leaverimage.b0.upaiyun.com/23624_o.jpg)

画好以后，选择文件-&gt;导出，按如下设置，
[![]({{BASE_PATH}}/images/5a4dc13cbe7e3ade5841d175eace74870fd42395.jpg "导出")](http://leaverimage.b0.upaiyun.com/23625_o.jpg)
=======
[![](/images/7bb961d46945780dd51bf752d4109eb6dfec9583.jpg "高级")](http://leaverimage.b0.upaiyun.com/23623_o.jpg)

然后选择混合模式为橡皮擦，就会擦去这个小圆，于是就只剩下一个圆环了。

[![](/images/47a442eae19c0450c9de4ef532d026c4608439f1.jpg "橡皮擦")](http://leaverimage.b0.upaiyun.com/23624_o.jpg)

画好以后，选择文件-&gt;导出，按如下设置，
[![](/images/5a4dc13cbe7e3ade5841d175eace74870fd42395.jpg "导出")](http://leaverimage.b0.upaiyun.com/23625_o.jpg)
>>>>>>> 换电脑之后重新备份

&nbsp;

会得到一个xaml文件，一会用

然后新建wpf项目，然后在解决方案资源管理器视图右键点击项目 导入现有项，把上一步的xaml文件导入

然后需要在app.xaml文件中进行设置，具体在&lt;Application.Resources&gt;标签内添加如下代码，中间那个文件名看情况而定。
<pre class="lang:default decode:true">        &lt;ResourceDictionary&gt;
            &lt;ResourceDictionary.MergedDictionaries&gt;
                &lt;ResourceDictionary Source="bystander.xaml"/&gt;
            &lt;/ResourceDictionary.MergedDictionaries&gt;
        &lt;/ResourceDictionary&gt;</pre>
&nbsp;

然后打开“MainWindow.xaml”文件的设计视图，点击窗体边缘以选中窗体，在属性面板中更改AllowsTransparency及WindowStyle属性。
AllowsTransparency 指示窗体是否支持透明。选中
WindowStyle指示窗体边框样式，设为 None 为无边框。

然后呢在 MainWindow.xaml文件中添加如下代码，
<pre class="lang:default decode:true">Background="{StaticResource back}"
MouseDown="Window_MouseDown"&gt;</pre>
最终代码是：
<pre class="lang:default decode:true">&lt;Window x:Class="WpfDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525" AllowsTransparency="True" WindowStyle="None" Background="{StaticResource back}" MouseDown="Window_MouseDown"&gt;
&lt;/Window&gt;</pre>
其中background那个是固定的，而MouseDown是为了给窗体写可以拖动的函数，函数名为Window_MouseDown你也可以自己制定

然后对着那个函数名点右键，如下图

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/2bda8545e86b4ffc4155d5c63505252bb57506bf.jpg "导航")](http://leaverimage.b0.upaiyun.com/23626_o.jpg)
=======
[![](/images/2bda8545e86b4ffc4155d5c63505252bb57506bf.jpg "导航")](http://leaverimage.b0.upaiyun.com/23626_o.jpg)
>>>>>>> 换电脑之后重新备份

导航到事件处理程序，然后在打开的函数里写上
<pre class="lang:default decode:true">if(e.ChangedButton==MouseButton.Left)
    this.DragMove();</pre>
拖动功能就实现了。

至于添加关闭按钮的，我就不写了，很简单，代码里都有。可以参考源文件。

工程源码下载：[WPFDemo](http://115.com/file/c2aq7abt#WpfDemo.7z)

参考：

[http://www.cnblogs.com/SkyD/archive/2008/07/13/1242044.html](http://www.cnblogs.com/SkyD/archive/2008/07/13/1242044.html)

[http://www.cnblogs.com/yinyao/archive/2011/05/23/2054056.html](http://www.cnblogs.com/yinyao/archive/2011/05/23/2054056.html)