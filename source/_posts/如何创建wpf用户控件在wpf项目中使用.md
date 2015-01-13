title: "如何创建WPF用户控件&在WPF项目中使用"
tags:
  - C＃
  - WPF
  - 大学
  - 编程
  - 软件
id: 2012
categories:
  - 我的翻译
date: 2012-10-14 15:19:24
---

作者给的Demo我合并了下。VS2010直接打开解决方案。二者都有。

[![]({{BASE_PATH}}/images/82f9a2b2665cf35702e252f3518a2f420361da11.png "00")](http://leaverimage.b0.upaiyun.com/28121_o.png)

**介绍**
本文展示在WPF中如何创建用户控件并且如果在WPF项目中使用。我将使用VS2008和C#来展示如何创建一个自定义的ToolTip

**背景**

这篇由[Sacha Barber](http://www.codeproject.com/KB/WPF/WPF_CustomerControl.aspx).写的和我的有点像。

**使用代码**

开始。首先，我们创建一个用户控件。因此，我们选择新建WPF用户控件类库（WPF User Control Library）。

[![]({{BASE_PATH}}/images/920a78558c2e8a260f9fd448739f68cd80e37108.png "01")](http://leaverimage.b0.upaiyun.com/28122_o.png)

现在。我们可以创建或者编辑XAML代码来创建自定义的用户控件了。我使用XAML来创建自定义的ToolTip。你想做什么随你。
<pre class="lang:default decode:true">&lt;UserControl 
    Name="UserControlToolTip"
    x:Class="CustomToolTip.UserControl1"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
    mc:Ignorable="d" RenderTransformOrigin="0,0" HorizontalAlignment="Left" 
	VerticalAlignment="Top" &gt;

    &lt;UserControl.RenderTransform&gt;
        &lt;TransformGroup&gt;
            &lt;ScaleTransform ScaleX="1" ScaleY="1"/&gt;
            &lt;SkewTransform AngleX="0" AngleY="0"/&gt;
            &lt;RotateTransform Angle="0"/&gt;
            &lt;TranslateTransform x:Name="UserControlToolTipXY" X="0" Y="0"/&gt;
        &lt;/TransformGroup&gt;
    &lt;/UserControl.RenderTransform&gt;

    &lt;Grid HorizontalAlignment="Center" VerticalAlignment="Center" 
	MinWidth="200" MinHeight="120"&gt;
    	&lt;Grid.RowDefinitions&gt;
    		&lt;RowDefinition Height="0.333*"/&gt;
    		&lt;RowDefinition Height="0.667*"/&gt;
    	&lt;/Grid.RowDefinitions&gt;
        &lt;Rectangle Fill="#FFFBFBFB" Stroke="#FF000000" RadiusX="10" RadiusY="10"
        	 RenderTransformOrigin="0.139,0.012" StrokeThickness="1" Grid.RowSpan="2"&gt;
            &lt;Rectangle.BitmapEffect&gt;
                &lt;DropShadowBitmapEffect Opacity="0.8"/&gt;
            &lt;/Rectangle.BitmapEffect&gt;
        &lt;/Rectangle&gt;
        &lt;Rectangle RadiusX="10" RadiusY="10" RenderTransformOrigin="0.139,0.012" 
        	StrokeThickness="10" Stroke="{x:Null}" 
	Margin="1,1,1,1" Grid.Row="0" Grid.RowSpan="2"&gt;
        	&lt;Rectangle.Fill&gt;
        		&lt;LinearGradientBrush EndPoint="0.5,1" StartPoint="0.5,0.725"&gt;
        			&lt;GradientStop Color="#00E6D9AA" Offset="0.487"/&gt;
        			&lt;GradientStop Color="#FFE6D9AA" Offset="0.996"/&gt;
        		&lt;/LinearGradientBrush&gt;
        	&lt;/Rectangle.Fill&gt;
        &lt;/Rectangle&gt;
        &lt;Rectangle RadiusX="10" RadiusY="10" RenderTransformOrigin="0.493,0.485" 
        	StrokeThickness="10" Stroke="{x:Null}" Grid.RowSpan="2" Margin="1,1,1,1"&gt;
        	&lt;Rectangle.Fill&gt;
        		&lt;LinearGradientBrush EndPoint="0.014,0.5" StartPoint="0.211,0.5"&gt;
        			&lt;GradientStop Color="#00E6D9AA" Offset="0.513"/&gt;
        			&lt;GradientStop Color="#FFE6D9AA" Offset="0.996"/&gt;
        		&lt;/LinearGradientBrush&gt;
        	&lt;/Rectangle.Fill&gt;
        &lt;/Rectangle&gt;
        &lt;Rectangle RadiusX="10" RadiusY="10" RenderTransformOrigin="0.493,0.485" 
        	StrokeThickness="10" Stroke="{x:Null}" Grid.RowSpan="2" Margin="1,1,1,1"&gt;
        	&lt;Rectangle.Fill&gt;
        		&lt;LinearGradientBrush EndPoint="0.493,0.002" StartPoint="0.493,0.33"&gt;
        			&lt;GradientStop Color="#00E6D9AA" Offset="0.513"/&gt;
        			&lt;GradientStop Color="#FFE6D9AA" Offset="0.996"/&gt;
        		&lt;/LinearGradientBrush&gt;
        	&lt;/Rectangle.Fill&gt;
        &lt;/Rectangle&gt;
        &lt;Rectangle RadiusX="10" RadiusY="10" RenderTransformOrigin="0.493,0.485" 
        	StrokeThickness="10" Stroke="{x:Null}" Grid.RowSpan="2" Margin="1,1,1,1"&gt;
        	&lt;Rectangle.Fill&gt;
        		&lt;LinearGradientBrush EndPoint="0.99,0.441" StartPoint="0.794,0.441"&gt;
        			&lt;GradientStop Color="#00E6D9AA" Offset="0.513"/&gt;
        			&lt;GradientStop Color="#FFE6D9AA" Offset="0.996"/&gt;
        		&lt;/LinearGradientBrush&gt;
        	&lt;/Rectangle.Fill&gt;
        &lt;/Rectangle&gt;
        &lt;TextBlock Text="TextBlock" TextWrapping="Wrap" x:Name="TextBlockToolTip" 
        	RenderTransformOrigin="0.5,0.5" Grid.Row="1" HorizontalAlignment="Left" 
            	VerticalAlignment="Center" Margin="20,0,0,20" /&gt;
        &lt;TextBlock Name="ToolTipTitle" HorizontalAlignment="Stretch" Margin="15,16,15,6.1" 
        	FontSize="14" Text="title" d:LayoutOverrides="Height" /&gt;
    &lt;/Grid&gt;
&lt;/UserControl&gt;</pre>
&nbsp;

同时。我们需要添加一些方法和属性来控制这些元素。
<pre class="lang:default decode:true">namespace CustomToolTip
{
    public partial class UserControl1 : UserControl
    {
        public UserControl1()
        {
            InitializeComponent();
        }

        public double UserControlToolTipX
        {
            get { return this.UserControlToolTipXY.X; }
            set { this.UserControlToolTipXY.X = value; }
        }

        public double UserControlToolTipY
        {
            get { return this.UserControlToolTipXY.Y; }
            set { this.UserControlToolTipXY.Y = value; }
        }

        public string UserControlTextBlockToolTip
        {
            get { return TextBlockToolTip.Text; }
            set { TextBlockToolTip.Text = value; }
        }

        public string UserControlToolTipTitle
        {
            get { return ToolTipTitle.Text; }
            set { ToolTipTitle.Text = value; }
        }
    }
}</pre>
&nbsp;

完成之后。我们按Shift+F6快捷键来生成dll文件。现在可以创建WPF项目并且使用我们的控件了。我们选择WPF应用程序项目

[![]({{BASE_PATH}}/images/ff20062e235c703bee9f99d1d63f2ddcf7ca18d0.png "02")](http://leaverimage.b0.upaiyun.com/28123_o.png)

然后。我们添加对我们的控件生成dll的引用（注意。其实还可以把这两个项目放在同一个解决方案里。然后此处添加对项目的引用就可以 –译者）

[![]({{BASE_PATH}}/images/f4ebc892c58f3c592813960b371677af584d9cac.png "03")](http://leaverimage.b0.upaiyun.com/28124_o.png)

我们将会在XAML窗体里使用自定义的用户控件。因此我们需要添加一些额外的XAML代码。我们在Window元素里添加下面一行。

[![]({{BASE_PATH}}/images/9ba6752518f547fcdfd695c9be8ed46283f3d0b3.png "04")](http://leaverimage.b0.upaiyun.com/28125_o.png)

至少。我们必须有一个Window 元素。。
<pre class="lang:default decode:true">&lt;Window x:Class="WpfApplication1.Window1"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:myToolTip="clr-namespace:CustomToolTip;assembly=CustomToolTip"
    Title="Window1" Height="600" Width="800"&gt;
&lt;/Window&gt;</pre>
好。现在我们在XAML中像这样使用控件

[![]({{BASE_PATH}}/images/ee363bd71413c52234278396f27f28bad40e1559.png "05")](http://leaverimage.b0.upaiyun.com/28126_o.png)

最后。我创建了如下的XAML代码：
<pre class="lang:default decode:true">&lt;Window x:Class="WpfApplication1.Window1"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:myToolTip="clr-namespace:CustomToolTip;assembly=CustomToolTip"
    Title="Window1" Height="600" Width="800"&gt;
    &lt;Grid x:Name="rootGrid" RenderTransformOrigin="0.5,0.5"&gt;
    	&lt;Grid.RenderTransform&gt;
    		&lt;TransformGroup&gt;
    			&lt;ScaleTransform ScaleX="1" ScaleY="1"/&gt;
    			&lt;SkewTransform AngleX="0" AngleY="0"/&gt;
    			&lt;RotateTransform Angle="0"/&gt;
    			&lt;TranslateTransform x:Name="rootGridXY" X="0" Y="0"/&gt;
    		&lt;/TransformGroup&gt;
    	&lt;/Grid.RenderTransform&gt;
        &lt;Rectangle Margin="26,34,496,374" Name="rectangle1" Stroke="Black" 
                   Fill="Coral" MouseLeave="rectangle_MouseLeave" 
		MouseMove="rectangle_MouseMove" /&gt;
        &lt;Rectangle Fill="Lavender" Margin="537,29,53,376" Name="rectangle2" 
                   Stroke="Black" MouseMove="rectangle_MouseMove" 
		MouseLeave="rectangle_MouseLeave" /&gt;
        &lt;Rectangle Fill="Peru" Margin="192,391,186,37.995" Name="rectangle3" 
                   Stroke="Black" MouseMove="rectangle_MouseMove" 
		MouseLeave="rectangle_MouseLeave" /&gt;
        &lt;myToolTip:UserControl1 UserControlTextBlockToolTip="Some texts" 
                                UserControlToolTipTitle="Title" 
                                Visibility="Hidden" 
                                x:Name="customToolTip" /&gt;
    &lt;/Grid&gt;
&lt;/Window&gt;</pre>
&nbsp;

再写一些方法来显示/隐藏我们的自定义ToolTip
<pre class="lang:default decode:true">namespace WpfApplication1
{
    /// &lt;summary&gt;
    /// Interaction logic for Window1.xaml
    /// &lt;/summary&gt;
    public partial class Window1 : Window
    {
        public Window1()
        {
            InitializeComponent();
        }

        private void rectangle_MouseLeave(object sender, MouseEventArgs e)
        {
            state = true;
            customToolTip.Visibility = Visibility.Hidden;
        }

        bool state = true;
        Random rand = new Random(DateTime.Now.Millisecond);

        private void rectangle_MouseMove(object sender, MouseEventArgs e)
        {
            if (state)
            {
                customToolTip.Visibility = Visibility.Visible;
                customToolTip.UserControlToolTipTitle = 
			(sender as Rectangle).Name.ToUpperInvariant();
                customToolTip.UserControlTextBlockToolTip = "";
                for (int i = 0; i &lt; rand.Next(1, 30); i++)
                    customToolTip.UserControlTextBlockToolTip += (sender as Rectangle).Name 
                    	+ "\t" + i.ToString() + "\n";
            }
            customToolTip.UserControlToolTipX = Mouse.GetPosition(this).X + 10;
            customToolTip.UserControlToolTipY = Mouse.GetPosition(this).Y + 10;
            state = false;
        }
    }
}</pre>
完了。

**Demo下载**
[downloadicon href=http://pan.baidu.com/share/link?shareid=83438&amp;uk=1493685990]CustomToolTipDemo[/downloadicon]

**许可**
本文包括源代码和文件在CPOL下授权。

&nbsp;

原文地址：[How-to-Creating-a-WPF-User-Control-using-it-in-a-W](http://www.codeproject.com/Articles/32825/How-to-Creating-a-WPF-User-Control-using-it-in-a-W)

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！