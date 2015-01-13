title: 一个可定制的WPF任务对话框
tags:
  - C＃
  - WPF
  - 学习
  - 总结
  - 收藏
  - 翻译
id: 1543
categories:
  - 学习笔记
date: 2012-09-24 08:03:17
---

今天实在看WPF揭秘的时候看到TaskDialog这个控件的。然后就去找了一下开源的代码。在codeproject上发现了这个，非常给力。。另外codeproject改版后很漂亮哦。
[![]({{BASE_PATH}}/images/ea5f46934b5478701eb79bb9b9b08381704b7996.jpg "one")](http://leaverimage.b0.upaiyun.com/27400_o.jpg)

### 介绍：

这是用WPF实现Vista上TaskDialog效果的代码。

### Messagbox消息框

通过调用重写的静态Show方法。TaskDialog就会表现的像一个Messagebox。他有四个文本类型的属性：Header（头部）, Content（内容）, Detail（更多）, 和 Footer（底部），其实Detail是一个折叠的区域， 而Header和Footer还有一个icon属性（HeaderIcon和FooterIcon），除此之外，Header还有Background（背景）和Foreground（前景）属性

<pre class="lang:c# decode:true " >// TaskDialog.Show方法签名
public static TaskDialogResult Show(
    string title, 
    string header, 
    string content, 
    string detail, 
    string footer, 
    TaskDialogButton button, 
    TaskDialogResult defaultResult, 
    TaskDialogIcon headerIcon, 
    TaskDialogIcon footerIcon, 
    Brush headerBackground, 
    Brush headerForeground)

// TaskDialog.Show 方法的一个例子
TaskDialog.Show("Task Dialog 测试",
     "消息框的标题文字",
     "消息框的内容部分. " +
     " 可以自适应内容.",
     "消息框的细节部分 " +
     "可以自适应内容",
     "消息框的底部.",
     TaskDialogButton.Ok,
     TaskDialogResult.None,
     TaskDialogIcon.Information,
     TaskDialogIcon.Shield,
     Brushes.White,
     Brushes.Navy);</pre> 

### 定制TaskDialog

使用静态的Show方法。Header, Content, Detail, 和Footer 就限制了只能传递字符串作为值了。
为了定义这个对话框，你先创建TaskDialog类的一个对象，然后分别设置一下各个属性，最后调用Show方法就可以了

<pre class="lang:c# decode:true " >// TaskDialog 实例化例子
TaskDialog dialog = new TaskDialog();
dialog.Title = "TaskDialog example";
dialog.HeaderIcon = TaskDialogIcon.Warning;
dialog.SystemSound = TaskDialogSound.Exclamation;
// header 属性设置
dialog.Header = "This is the Header.";
dialog.HeaderBackground = Brushes.DarkGray;
dialog.HeaderForeground = Brushes.White;
// Content, Detail 和 Footer属性设置
dialog.Content = "This is the content";
dialog.Detail = "This is the detail";
dialog.Footer = "this is the Footer";
dialog.Show();</pre> 

TaskDialog控件派生自HeaderedContentControl类，因为从HeaderedContentControl类可以获得Header和Content属性，TaskDialog仅仅是添加了Detail和Footer属性，这些属性是Object类型，并且有他们自己的template（模板）属性HeaderTemplate, ContentTemplate, DetailTemplate, 和 FooterTemplate)，TaskDialog类对于文本内容有着缺省的数据模板，当然你也可以用那四个模板来替换，这样你就可以以你喜欢的任何方式来格式化文本了。下面这个图展示了通过斜体和下划线来格式化文本。

[![]({{BASE_PATH}}/images/05a7c6d00f37749a2b770d0b1f1ebaab51528de0.jpg "two")](http://leaverimage.b0.upaiyun.com/27401_o.jpg)
图2

<pre class="lang:c# decode:true " >//为上面这个图的content属性的 DataTemplate 模板 
&lt;DataTemplate x:Key="_customContentDataTemplate"&gt;
   &lt;TextBlock Text="{Binding Content, 
        RelativeSource={RelativeSource FindAncestor, 
                        AncestorType={x:Type Controls:TaskDialog}}}" 
        FontStyle="Italic" 
        TextDecorations="Underline" 
        TextWrapping="Wrap"/&gt;
&lt;/DataTemplate&gt;</pre> 

因为Header，Content，Detail和Footer是object类型，因此不再受到只能是文本的限制了，你可以防止你喜欢的任何类型到TaskDialog，下面这个例子中的TaskDialog是不是很像UAC的提示呢。这里Content属性是一个UserControl类型，放置了一个图片和一些文本还有两个CommandButtons（都是普通的按钮。。不过添加了一些定制的样式，再加了Header属性）

[![]({{BASE_PATH}}/images/8d51c0c800cce2a90b815b20432b5cd520562e92.jpg "three")](http://leaverimage.b0.upaiyun.com/27402_o.jpg)
图三

一个模拟vista的文件拷贝窗口的TaskDialog。。

[![]({{BASE_PATH}}/images/14c4b78afe3921ae6648d96cdd78718930914d07.jpg "four")](http://leaverimage.b0.upaiyun.com/27403_o.jpg)
图四

### 额外的一些有意思的属性

下面是TaskDialog暴露的属性列表（按字母表排序）
Button1Text: string类型，当TaskDialogButton属性被设置为Custom的时候，用来设置按钮的文字
Button2Text: 同上
Button3Text: 同上
DefaultResult: TaskDialogResult类型. 设置缺省的按钮
IsButton1Enabled: bool类型. 获取或设置按钮的Enabled状态
IsButton2Enabled: 同上
IsButton3Enabled: 同上
IsCloseButtonEnabled: bool类型. 获取或设置窗口关闭按钮的Enabled状态 (缺省为false)
IsExpanded: bool类型，获取或是设置Detail部分的可见性。
IsModal: bool类型，获取或是设置对话框是不是模态的
ShowInTaskBar: bool类型，获取或是设置对话框是不是显示在任务栏中
SystemSound: TaskDialogSound类型. 设置当对话框显示的时候播放的声音
TaskDialogButton: TaskDialogButton类型. 显示在对话框上的按钮可以是None, Ok, OkCancel, YesNo, YesNoCancel, or Custom (查看上面提到的按钮设置)
Title: string类型，对话框的标题
ToggleButtonTexts:  TaskDialogToggleButtonTexts提供了两个属性来设置toggle按钮的这个属性，当toggle 按钮折叠或是展开的时候 (缺省是'Show Details' 和'Hide Details').
TopMost: 设置对话框是否置顶

### Demo

下载[TaskDialog例子](http://pan.baidu.com/share/link?shareid=61407&uk=1493685990)，查看Window1.xaml中按钮的单击事件，来看TaskDialog是如何被创建的。。

版权：本文，包括源代码和文件，在CPOL下授权。

原文地址：[A-Customizable-WPF-TaskDialog](http://www.codeproject.com/Articles/30649/A-Customizable-WPF-TaskDialog)

著作权声明：本文由http://leaver.me 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！