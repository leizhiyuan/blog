title: 一步步教你制作WPF圆形玻璃按钮
tags:
  - C＃
  - WPF
  - 大学
  - 学习
  - 翻译
  - 设计
  - 软件
id: 1980
categories:
  - 我的翻译
date: 2012-10-12 09:31:00
---

[![]({{BASE_PATH}}/images/491dd2dbc974b8c78aee2f36006e65ad3e5533f2.jpg "1")](http://leaverimage.b0.upaiyun.com/28015_o.jpg)
**1.介绍**

从我开始使用vista的时候，我就非常喜欢它的圆形玻璃按钮。WPF最好的一个方面就是允许自定义任何控件的样式。用了一段时间的Microsoft Expression Blend后。我做出了这个样式。我觉得做的还行。因为。我决定分享。如我所说。我使用Microsoft Expression Blend来做。但是。我也是用XAML编辑器--Kaxaml。

**2.概述**

玻璃按钮样式包含了三层。组织了玻璃效果（Glass Effect）和一个ContentPresenter 来存储按钮的内容。所有的这些层都在一个最外层的Grid里。当鼠标放到按钮上，按下去的时候也定义了一些触发器（Triggers），来增加一些交互。

我把这个样式做成了资源文件。但是这个Key可以删除，来使得所有的按钮都是这个效果。

好我们来看一下这些层次。这些被广泛应用在微软产品中的按钮。

**3.按钮层次 **

**3.1背景层**

第一层是一个椭圆。其实是一个canvas，一会在上面画反射和折射层，填充的颜色和按钮的背景（Background）关联。

下面是Blend中的截图

图2
[![]({{BASE_PATH}}/images/2443ed6bf6b648418ee018a527c9a76eaf277716.png "2")](http://leaverimage.b0.upaiyun.com/28012_o.png)
<pre class="lang:default decode:true">&lt;!-- Background Layer --&gt;
&lt;Ellipse Fill="{TemplateBinding Background}"/&gt;</pre>
<!-- Background Layer -->
**3.1.1折射层**

第二层模拟了光从上到下的折射。被放在反射层之前是因为，要达到反光玻璃的效果，反射层必须在按钮的中间某处有一个硬边缘。这一层实际上是另一个椭圆。但是这次。我们使用一个径向渐变（白色-透明）的填充。来模拟光的折射。渐变开始于第一层底部的中央。结束于上面的中间。然而。为了降低折射光的强度。渐变还是开始于椭圆的底部再下一点为好。可以从图上和代码里清晰的看到。
[![]({{BASE_PATH}}/images/9d0d815903ff370933cfca9fcd669ae09996e045.png "3")](http://leaverimage.b0.upaiyun.com/28013_o.png)
<pre class="lang:default decode:true ">&lt;!-- Refraction Layer --&gt;
&lt;Ellipse x:Name="RefractionLayer"&gt;
  &lt;Ellipse.Fill&gt;
  &lt;RadialGradientBrush GradientOrigin="0.496,1.052"&gt;
    &lt;RadialGradientBrush.RelativeTransform&gt;
      &lt;TransformGroup&gt;
        &lt;ScaleTransform CenterX="0.5" 
          CenterY="0.5" ScaleX="1.5" ScaleY="1.5"/&gt;
        &lt;TranslateTransform X="0.02" Y="0.3"/&gt;
      &lt;/TransformGroup&gt;
    &lt;/RadialGradientBrush.RelativeTransform&gt;
    &lt;GradientStop Offset="1" Color="#00000000"/&gt;
    &lt;GradientStop Offset="0.4" Color="#FFFFFFFF"/&gt;
    &lt;/RadialGradientBrush&gt;
  &lt;/Ellipse.Fill&gt;
&lt;/Ellipse&gt;</pre>
&nbsp;

<!-- Refraction Layer -->

**3.1.2反射层**

第三层是光的反射层。是最难的部分。问题是反射效果不能使用任何标准的形状来画。因此。使用路径（Path）来画反射区域。当时。手工画也是可以的。但老实说。手工画图实在没什么可享受的（除非你是一个艺术家，或者有一个数位板），无论如何。我现在MS Blend中华好一个椭圆并转换成一个路径，然后我使用贝塞尔曲线点调整得到平滑的路径，你可以添加渐变到一个复杂的Path对象上。就像你对其他与定义的图形，比如椭圆，矩形所做的一样。为了得到光泽反射。我额每年需要一个透明-白色的径向渐变填充，从路径的底部开始（也就是按钮的中间某处），结束在顶部。我想如果我是一个艺术家。我会让渐变更准一点。可是我不是。因此。就这样。因为我们要把我们的按钮放在一个Grid里。所有我们设置VerticalAlignment="Top" 这样反射区域在按钮的中间的结束了。

图三
[![]({{BASE_PATH}}/images/a837470f0822bf1b924293855638d696dc4dba4a.png "3")](http://leaverimage.b0.upaiyun.com/28014_o.png)
<pre class="lang:default decode:true ">&lt;!-- Reflection Layer --&gt;
&lt;Path x:Name="ReflectionLayer" VerticalAlignment="Top" Stretch="Fill"&gt;
  &lt;Path.RenderTransform&gt;
    &lt;ScaleTransform ScaleY="0.5" /&gt;
  &lt;/Path.RenderTransform&gt;
  &lt;Path.Data&gt;
    &lt;PathGeometry&gt;
      &lt;PathFigure IsClosed="True" StartPoint="98.999,45.499"&gt;
        &lt;BezierSegment Point1="98.999,54.170" Point2="89.046,52.258" 
           Point3="85.502,51.029"/&gt;
        &lt;BezierSegment IsSmoothJoin="True" Point1="75.860,47.685" 
           Point2="69.111,45.196" Point3="50.167,45.196"/&gt;
        &lt;BezierSegment Point1="30.805,45.196" Point2="20.173,47.741" 
           Point3="10.665,51.363"/&gt;
        &lt;BezierSegment IsSmoothJoin="True" Point1="7.469,52.580" 
           Point2="1.000,53.252" Point3="1.000,44.999"/&gt;
        &lt;BezierSegment Point1="1.000,39.510" Point2="0.884,39.227" 
           Point3="2.519,34.286"/&gt;
        &lt;BezierSegment IsSmoothJoin="True" Point1="9.106,14.370" 
           Point2="27.875,0" Point3="50,0"/&gt;
        &lt;BezierSegment Point1="72.198,0" Point2="91.018,14.466" 
           Point3="97.546,34.485"/&gt;
        &lt;BezierSegment IsSmoothJoin="True" Point1="99.139,39.369" 
           Point2="98.999,40.084" Point3="98.999,45.499"/&gt;
      &lt;/PathFigure&gt;
    &lt;/PathGeometry&gt;
  &lt;/Path.Data&gt;
  &lt;Path.Fill&gt;
    &lt;RadialGradientBrush GradientOrigin="0.498,0.526"&gt;
      &lt;RadialGradientBrush.RelativeTransform&gt;
        &lt;TransformGroup&gt;
          &lt;ScaleTransform CenterX="0.5" 
            CenterY="0.5" ScaleX="1" ScaleY="1.997"/&gt;
          &lt;TranslateTransform X="0" Y="0.5"/&gt;
        &lt;/TransformGroup&gt;
      &lt;/RadialGradientBrush.RelativeTransform&gt;
      &lt;GradientStop Offset="1" Color="#FFFFFFFF"/&gt;
      &lt;GradientStop Offset="0.85" Color="#92FFFFFF"/&gt;
      &lt;GradientStop Offset="0" Color="#00000000"/&gt;
    &lt;/RadialGradientBrush&gt;
  &lt;/Path.Fill&gt;
&lt;/Path&gt;</pre>
&nbsp;

<!-- Reflection Layer -->

最后。我添加一个ContentPresenter 到按钮中间。经验告诉我，内容区域再向下一个像素会使得按钮看起来更漂亮。因此，在这里我用了margin属性（注意。因为内容区域在Grid的中间（Center）。所以2个像素的top实际上是向下移动了一个像素 ）

好了。最后在Blend中看起来大概是这样

图4
[![]({{BASE_PATH}}/images/19fbafa7ad1cde09eafc0f6a70ac27b576e6302c.png "4")](http://leaverimage.b0.upaiyun.com/28011_o.png)

**4.添加一些交互性**

**4.1鼠标悬停效果**

为了有鼠标悬停效果，我们需要增加光源的亮度。因此。我们为IsMouseOver 事件定义一个触发器，复制并且粘贴反射和折射层的渐变设置代码。对于折射层。我仅仅移动了渐变的起点向上了一点。在反射层中。我改变了渐变停止点。使不透明的白色多一点。
<pre class="lang:default decode:true">&lt;Trigger Property="IsMouseOver" Value="True"&gt;
  &lt;Setter TargetName="RefractionLayer" Property="Fill"&gt;
    &lt;Setter.Value&gt;
      &lt;RadialGradientBrush GradientOrigin="0.496,1.052"&gt;
        &lt;RadialGradientBrush.RelativeTransform&gt;
          &lt;TransformGroup&gt;
            &lt;ScaleTransform CenterX="0.5" CenterY="0.5" 
               ScaleX="1.5" ScaleY="1.5"/&gt;
            &lt;TranslateTransform X="0.02" Y="0.3"/&gt;
          &lt;/TransformGroup&gt;
        &lt;/RadialGradientBrush.RelativeTransform&gt;
      &lt;GradientStop Offset="1" Color="#00000000"/&gt;
      &lt;GradientStop Offset="0.45" Color="#FFFFFFFF"/&gt;
      &lt;/RadialGradientBrush&gt;
    &lt;/Setter.Value&gt;
  &lt;/Setter&gt;
  &lt;Setter TargetName="ReflectionLayer" Property="Fill"&gt;
    &lt;Setter.Value&gt;
      &lt;RadialGradientBrush GradientOrigin="0.498,0.526"&gt;
        &lt;RadialGradientBrush.RelativeTransform&gt;
          &lt;TransformGroup&gt;
            &lt;ScaleTransform CenterX="0.5" CenterY="0.5" 
               ScaleX="1" ScaleY="1.997"/&gt;
            &lt;TranslateTransform X="0" Y="0.5"/&gt;
          &lt;/TransformGroup&gt;
        &lt;/RadialGradientBrush.RelativeTransform&gt;
        &lt;GradientStop Offset="1" Color="#FFFFFFFF"/&gt;
        &lt;GradientStop Offset="0.85" Color="#BBFFFFFF"/&gt;
        &lt;GradientStop Offset="0" Color="#00000000"/&gt;
      &lt;/RadialGradientBrush&gt;
    &lt;/Setter.Value&gt;
  &lt;/Setter&gt;
&lt;/Trigger&gt;</pre>
&nbsp;

**4.2鼠标点击效果**
对于IsPressed 事件，需要降低光。因此。反向操作即可。折射层中光源下一点。反射层中渐变停止点更加透明一些。
<pre class="lang:default decode:true ">&lt;Trigger Property="IsPressed" Value="True"&gt;
  &lt;Setter TargetName="RefractionLayer" Property="Fill"&gt;
    &lt;Setter.Value&gt;
      &lt;RadialGradientBrush GradientOrigin="0.496,1.052"&gt;
        &lt;RadialGradientBrush.RelativeTransform&gt;
          &lt;TransformGroup&gt;
            &lt;ScaleTransform CenterX="0.5" CenterY="0.5" 
               ScaleX="1.5" ScaleY="1.5"/&gt;
            &lt;TranslateTransform X="0.02" Y="0.3"/&gt;
          &lt;/TransformGroup&gt;
        &lt;/RadialGradientBrush.RelativeTransform&gt;
        &lt;GradientStop Offset="1" Color="#00000000"/&gt;
        &lt;GradientStop Offset="0.3" Color="#FFFFFFFF"/&gt;
      &lt;/RadialGradientBrush&gt;
    &lt;/Setter.Value&gt;
  &lt;/Setter&gt;
  &lt;Setter TargetName="ReflectionLayer" Property="Fill"&gt;
    &lt;Setter.Value&gt;
      &lt;RadialGradientBrush GradientOrigin="0.498,0.526"&gt;
        &lt;RadialGradientBrush.RelativeTransform&gt;
          &lt;TransformGroup&gt;
            &lt;ScaleTransform CenterX="0.5" CenterY="0.5" 
               ScaleX="1" ScaleY="1.997"/&gt;
            &lt;TranslateTransform X="0" Y="0.5"/&gt;
          &lt;/TransformGroup&gt;
        &lt;/RadialGradientBrush.RelativeTransform&gt;
        &lt;GradientStop Offset="1" Color="#CCFFFFFF"/&gt;
        &lt;GradientStop Offset="0.85" Color="#66FFFFFF"/&gt;
        &lt;GradientStop Offset="0" Color="#00000000"/&gt;
      &lt;/RadialGradientBrush&gt;
    &lt;/Setter.Value&gt;
  &lt;/Setter&gt;
&lt;/Trigger&gt;</pre>
&nbsp;

再说一次。渐变停止点的值靠经验选的。我也不能给出精确的值。

5.使用代码

为了使用这个样式。把定义在GlassButton.xaml 里的样式资源文件并不到你的窗体/页里。然后设置按钮的样式为{StaticResource GlassButton}. 为了设置按钮的颜色。使用Background属性即可。

&nbsp;
<pre class="lang:default decode:true ">&lt;Window x:Class="GlassButton.Window1"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Glass Buttons" Height="228" Width="272"&gt;

  &lt;Window.Resources&gt;
    &lt;ResourceDictionary&gt;
      &lt;ResourceDictionary.MergedDictionaries&gt;
        &lt;ResourceDictionary Source="Resources\GlassButton.xaml"/&gt;
      &lt;/ResourceDictionary.MergedDictionaries&gt;
    &lt;/ResourceDictionary&gt;
  &lt;/Window.Resources&gt;

  &lt;Grid&gt; 
    &lt;Button Style="{StaticResource GlassButton}" Width="50" 
       Height="50" Background="#FF660707" Margin="10"/&gt; 
  &lt;/Grid&gt;
&lt;/Window&gt;</pre>
&nbsp;

**6.Demo下载**
[downloadicon href=http://pan.baidu.com/share/link?shareid=81378&uk=1493685990]Demo与源代码下载[/downloadicon]
**7.许可**

本文包括源代码和文件在CPOL下授权。

原文地址：[a-style-for-round-glassy-wpf-buttons](http://www.codeproject.com/articles/32257/a-style-for-round-glassy-wpf-buttons)

&nbsp;

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！