title: 自定义WPF LinkLabel 控件
tags:
  - C＃
  - WPF
  - 大学
  - 编程
  - 软件
id: 1955
categories:
  - 我的翻译
date: 2012-10-11 13:24:02
---

WPF里是没有LinkLabel控件的。因此我自己写一个。首先。我们看一下WPF中什么类似的组件可以实现这个链接功能

如果你想要模拟一个LinkLabel控件。你可以在TextBlock里使用内联的Hyperlink。像下面这样
<pre class="lang:default decode:true">&lt;TextBlock&gt;
    &lt;Hyperlink&gt;
        &lt;Run Text="Test link"/&gt;
    &lt;/Hyperlink&gt;
&lt;/TextBlock&gt;</pre>
你可以使用Label控件。加一个内联的HyperLink，但是我认为TextBlock更好。因为你可以在Expression Blend 中通过[InlineCollection](http://msdn2.microsoft.com/en-us/library/system.windows.documents.inlinecollection.aspx "InlineCollection")  编辑所有子元素的属性

图1

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/2fbf37d3f59121424e1f68d6ac6e2a461f8e1ab9.png "1")](http://leaverimage.b0.upaiyun.com/27987_o.png)
=======
[![](/images/2fbf37d3f59121424e1f68d6ac6e2a461f8e1ab9.png "1")](http://leaverimage.b0.upaiyun.com/27987_o.png)
>>>>>>> 换电脑之后重新备份
虽然这种方法也行，但是我还是不太喜欢。因为我觉得我还是写一个类似windows窗体程序中的LinkLabel控件。然后我就做了。首先看一下控件的样子

图2

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/b4438242681c5adebadef5aff0f7f2145be09a43.gif "2")](http://leaverimage.b0.upaiyun.com/27988_o.gif)
=======
[![](/images/b4438242681c5adebadef5aff0f7f2145be09a43.gif "2")](http://leaverimage.b0.upaiyun.com/27988_o.gif)
>>>>>>> 换电脑之后重新备份

第一个是默认的LinkLabel控件。第二个是LinkLabelBehavior 属性被设置为HoverUnderline ，第三个的Foreground和 HoverForeground 属性都使用了自定的颜色。

&nbsp;

LinkLabel控件支持的属性

1.Foreground和 HoverForeground属性

允许自定义这两个属性的值

2.LinkLabelBehavior 属性

允许设置下划线的显示与否

3.自定义HyperlinkStyle 属性

你可以使用这个属性给超链接设置自定义的样式。如果你已经自定了Foreground和 HoverForeground。则会被覆盖。

Url 超链接的目标

所有这些属性都继承自标准的System.Windows.Controls.Label 控件

通过Blend/Xaml设置这些属性很简单
<pre class="lang:default decode:true">&lt;ThemedControlLibrary:LinkLabel Content="Link Label" FontSize="22"/&gt;
&lt;ThemedControlLibrary:LinkLabel Content="Link Label" LinkLabelBehavour="HoverUnderline" /&gt;
&lt;ThemedControlLibrary:LinkLabel Foreground="#FF847901" HoverForeground="#FF06C8F2" Content="Link Label" LinkLabelBehavour="NeverUnderline"/&gt;</pre>
图三

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/a3a5126a4aba4698c3916d5a9e92f4daa7d77b97.png "3")](http://leaverimage.b0.upaiyun.com/27989_o.png)
=======
[![](/images/a3a5126a4aba4698c3916d5a9e92f4daa7d77b97.png "3")](http://leaverimage.b0.upaiyun.com/27989_o.png)
>>>>>>> 换电脑之后重新备份

然后是控件的使用方法。仅仅添加命名空间到xaml中。然后使用就行了。
<pre class="lang:default decode:true">&lt;Window
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    x:Class="DemoApplication.Window1"
    Title="DemoApplication" Height="300" Width="300"
    xmlns:ThemedControlsLibrary="clr-namespace:ThemedControlsLibrary;assembly=ThemedControlsLibrary"
    &gt;
    &lt;Grid&gt;
        &lt;ThemedControlsLibrary:LinkLabel HorizontalAlignment="Left" VerticalAlignment="Top" Content="LinkLabel"/&gt;
    &lt;/Grid&gt;
&lt;/Window&gt;</pre>
&nbsp;

控件的完整代码很简单。就定义一下需要的属性，和控制这些属性应该显示在Blend中的（category）目录位置就行了。

&nbsp;
<pre class="lang:default decode:true">public class LinkLabel : Label
{
    private const string _linkLabel = "LinkLabel";

    public static readonly DependencyProperty UrlProperty = DependencyProperty.Register("Url", typeof(Uri), typeof(LinkLabel));

    [Category("Common Properties"), Bindable(true)]
    public Uri Url
    {
        get { return GetValue(UrlProperty) as Uri; }
        set { SetValue(UrlProperty, value); }
    }

    public static readonly DependencyProperty HyperlinkStyleProperty = DependencyProperty.Register("HyperlinkStyle", typeof(Style),
            typeof(LinkLabel));

    public Style HyperlinkStyle
    {
        get { return GetValue(HyperlinkStyleProperty) as Style; }
        set { SetValue(HyperlinkStyleProperty, value); }
    }

    public static readonly DependencyProperty HoverForegroundProperty = DependencyProperty.Register("HoverForeground", typeof(Brush),
            typeof(LinkLabel));

    [Category("Brushes"), Bindable(true)]
    public Brush HoverForeground
    {
        get { return GetValue(HoverForegroundProperty) as Brush; }
        set { SetValue(HoverForegroundProperty, value); }
    }

    public static readonly DependencyProperty LinkLabelBehavourProperty = DependencyProperty.Register("LinkLabelBehavour",            typeof(LinkLabelBehaviour),     typeof(LinkLabel));

    [Category("Common Properties"), Bindable(true)]
    public LinkLabelBehaviour LinkLabelBehavour
    {
        get { return (LinkLabelBehaviour)GetValue(LinkLabelBehavourProperty); }
        set { SetValue(LinkLabelBehavourProperty, value); }
    }

    static LinkLabel()
    {
        FrameworkElement.DefaultStyleKeyProperty.OverrideMetadata(
            typeof(LinkLabel),
            new FrameworkPropertyMetadata(typeof(LinkLabel)));
    }

}</pre>
&nbsp;

为了使得内容可以绑定。我使用了Filipe Fortes 实现的BindableRun 我添加BindableRun  到控件模板中
<pre class="lang:default decode:true">&lt;local:BindableRun BoundText="{Binding RelativeSource= {RelativeSource TemplatedParent}, Path=Content}"/&gt;</pre>
&nbsp;

为了支持主题显示。应该在工程里有个Themes文件夹。包含控件的样式。LinkLabel只有一个样式文件Generic.xaml，因为超链接样式不管当前主题是什么。都是一样的。我不会包含LinkLabel 的模板。

&nbsp;

多看源代码。下载并且开始使用吧。

[示例和源码下载](http://pan.baidu.com/share/link?shareid=80913&amp;uk=1493685990)

[http://www.blagoev.com/blog/post/building-a-wpf-linklabel-control.aspx](http://www.blagoev.com/blog/post/building-a-wpf-linklabel-control.aspx)