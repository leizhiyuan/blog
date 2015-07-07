title: AvalonDock 2.0入门指南第一部分
tags:
  - C＃
  - WPF
  - 大学
  - 学习
  - 翻译
id: 1693
categories:
  - 我的翻译
date: 2012-09-26 16:38:26
---

[AvalonDock](http://avalondock.codeplex.com) 2.0可以用来为WPF创建一个类似Visual Studio的界面，深入理解如何使用AvalonDock进行开发是很重要的。

在这个入门指南里，我将演示如何开始使用AvalonDock，下面的文章都是基于2.0版本的。并且不能用于早期的版本。

AvalonDock是一个组合的布局模型，很多的控件都在视图上显示，一个DockingManager 类也显示在停靠区，用于可以拖拽文档和工具。

从下面这个截图中我们可以理解AvalonDock组件

[![](/images/38cf4425c7a05bde89671d9f06c99e6626985373.png "ADTutorial")](http://leaverimage.b0.upaiyun.com/27544_o.png)
**DockingManager** 这是AvalonDock中的核心控件，它将包含的窗格排序，处理飞出的窗格，还有浮动的窗口。在上面这个图中，DockingManager 对象包含了所有空间（WPF控件），从顶部的工具栏到底部的状态栏都算。同时。DockingManager 也可以处理保存和恢复布局。

**LayoutPanel** 这个面板用来管理在一个方向上的子窗口（通过Orientation属性来选择方向），并且在它们之间添加了一个大小调节控件，在一个Orientation属性是Horizontal（水平）的LayoutPanel 上，排列了三个窗格。一个LayoutAnchorablePane在左，一个LayoutDocumentPane在中间。一个LayoutDockablePane在右边。

**LayoutDockablePane**  这个布局元素包含一个LayoutAnchorable对象的集合。通过它用来管理想TabControl这样的控件，在上面的截图中，LayoutDockablePanes是在左边的'Strumenti' 和 'Progetti' (工具和项目)  和在右边的'Classi' 和 'Proprieta'' (类视图和属性视图)的容器，一个LayoutDockablePane可以自动隐藏，就像'Errori'(错误)和'Lista Azioni'(操作列表) and 'Uscita'(输出)。并且LayoutDockablePane可以被拖动到DockingManager上，成为一个浮动窗口或者附着到它的父控件DockingManager的边缘上。

**LayoutDocumentPane** 通常包含文档（DocumentContent类型）的一种窗格，但是其实也可以包含像上面提到的工具视图和类视图这样的DockableContents。在一个文档里。LayoutDocumentPane 被放置在ResizingPanel（水平方向）里。ResizingPanel则是上卖弄提到的在两个DockablePane中间的区域。注意。文档窗格是不能被移动的。

**LayoutAnchorable** 一个停靠内容，是软件控件的容器，总是被包含在一个窗格里（LayoutAnchorablePane或是LayoutDocumentPane），在截图里。LayoutAnchorable是一类对象（包含一个SharpDevelop对象），工具对象，但是错误窗口（它处于自动隐藏状态，被好办在一个自动隐藏窗格里）不是。LayoutAnchorable就像它名字所暗示的那样。可以被从他的容器窗格里拖走。然后重新放置在一个存在的窗格里。或者是放置在父DockingManager的边缘，或者是放置在一个浮动窗口里（LayoutAnchorableFloatingWindow）。

**LayoutDocument** 是一个仅可以被寄宿到LayoutDocumentPane的内容。它是一个特殊的内容，因为不能被停靠到边缘。仅能被放置到LayoutDocumentPane里。或者浮动在一个LayoutDocumentFloatingWindow窗口里。在途中，DocumentContent对象是program.cs' 或 'MainForm.cs' 文件视图

**LayoutFloatingWindow **，是一个包含内容的窗口，当被拖动到一个DockingManager上面的时候，LayoutFloatingWindow（LayoutAnchorableFloatingWindow和LayoutDocumentFloatingWindow继承自他）集成在Window，总是包含一个窗格（LayoutAnchorablePane或是LayoutDocumentPane），窗格包含更多的内容（LayoutAnchorable或LayoutDocument），当用户对一个内容或是DockablePane执行拖拽，或者直接手工使用代码调用LayoutContent.Float()方法 LayoutFloatingWindow就被直接从DockingManager创建出来了。

**LayoutPane**  一个基类，LayoutDockablePane和LayoutDocumentPane继承自它。它为他们提供了一些共有的属性和方法。

**LayoutContent** 是LayoutAnchorable 和LayoutAnchorable类的父类。提供了共有的属性和方法。

理解布局元素是一些属于布局模型的类而不是属于视图是很重要的。因为，他们不是继承自FrameworkElement类，取而代之。AvalonDock包含了另一些类来展示这些组件的视图。这些类通常被命名为相关联的类名+Control字串。举个例子，在布局里创建的LayoutAnchorable创建了一个LayoutAnchorableControl（继承自FrameworkElement的类），并且绑定了LayoutAnchorableControl.Model 到这个LayoutAnchorable对象上。

每一个被创建的视图控件总是有着Model属性的布局元素。因此，重新设计一个相关视图控件的样式是可以的。

为了开始创建一个新的.net 4/.net 4.5的解决方案。并且添加对AvalonDock.dll的引用（可以直接添加也可以使用NuGet），然后添加AD命名空间到MainWindow.xaml中。

在根Grid下放置DockingManager组件和一个简单的布局。

<pre class="lang:xhtml decode:true " >&lt;Window x:Class="MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:avalonDock="http://avalondock.codeplex.com"
        Title="MainWindow" Height="434" Width="684"&gt;
    &lt;Grid&gt;
        &lt;avalonDock:DockingManager x:Name="dockingManager"&gt;
            &lt;avalonDock:LayoutRoot&gt;
                &lt;avalonDock:LayoutPanel Orientation="Horizontal"&gt;
                    &lt;avalonDock:LayoutDocumentPane/&gt;
                    &lt;avalonDock:LayoutAnchorablePane DockWidth="150"&gt;
                        &lt;avalonDock:LayoutAnchorable Title="Sample Tool Pane"&gt;
                            &lt;TextBox/&gt;
                        &lt;/avalonDock:LayoutAnchorable&gt;
                    &lt;/avalonDock:LayoutAnchorablePane&gt;
                &lt;/avalonDock:LayoutPanel&gt;
                &lt;avalonDock:LayoutRoot.LeftSide&gt;
                    &lt;avalonDock:LayoutAnchorSide&gt;
                        &lt;avalonDock:LayoutAnchorGroup&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Autohidden Content"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                        &lt;/avalonDock:LayoutAnchorGroup&gt;
                    &lt;/avalonDock:LayoutAnchorSide&gt;
                &lt;/avalonDock:LayoutRoot.LeftSide&gt;
            &lt;/avalonDock:LayoutRoot&gt;
        &lt;/avalonDock:DockingManager&gt;
    &lt;/Grid&gt;
&lt;/Window&gt;
</pre> 

DockingManager是AvalonDock的核心类，他的责任就是创建管理布局。布局被定义成一个ILayoutElement的树。树的根由LayoutRoot类指定。LayoutRoot由一些基本的子树来构成。。

1.根面板的 根属性指向主 LayoutPanel，也是LayoutRoot的内容属性。

2.大体上讲，RightSide/LeftSide/TopSide/BottonSide是LayoutAnchorGroup对象的集合属性，他们表示了停靠管理器的四个边。停靠管理器的锚通常是隐藏的。当用户移动鼠标到这些区域的时候。就会在自动隐藏的窗口里显示出来。

3.FloatingWindows属性是FloatingWindow的集合。一个浮动窗口当用户拖动一个窗格（LayoutAnchorable或是LayoutDocument）的时候就被创建出来，这个集合可以被AvalonDock自动更新，但是用户依然可以通过调用LayoutContent的Float()方法来创建一个浮动窗口。

Hidden是一个Anchorable（停靠）对象的集合。默认情况下，当用户点击一个LayoutAnchorable对象的关闭按钮，AvalonDock隐藏它：通过从布局里移除停靠元素，并且把他放入Hidden集合，当用户想要再一次显示的时候，AD又把这个内容从隐藏集合里删除重新显示在他被隐藏的那个窗格里。

当使用布局树的时候，程序员可以创建任何复杂的界面，LayoutAnchorablePane的DockWidth和DockHeight属性可以被用来设置窗格的初始宽度和高度。而LayoutDocumentPane类通常则填满可用的空间。AvalonDock管理内容元素的宽度和高度以使得可以使用所有的可用空间。因此如果一个LayoutAnchorablePane被放置在一个LayoutPanel里，为LayoutAnchorablePane使用一个固定尺寸而为LayoutDocumentPane使用一个比例长度。也就是说为停靠对象使用了比例长度。

一个LayoutDocumentGroup/ LayoutAnchorableGroup类可以被用来包含更多的LayoutDocumentPane/ LayoutAnchorablePane，举个例子，让我们来改变上面的例子来实现更复杂的例子。

<pre class="lang:xhtml decode:true " >&lt;Window x:Class="AvalonDock2Tutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:avalonDock="http://avalondock.codeplex.com"
        Title="MainWindow" Height="350" Width="525"&gt;
    &lt;Grid&gt;
        &lt;avalonDock:DockingManager x:Name="dockingManager"&gt;
            &lt;avalonDock:LayoutRoot&gt;
                &lt;avalonDock:LayoutPanel Orientation="Horizontal"&gt;
                    &lt;avalonDock:LayoutDocumentPaneGroup&gt;
                        &lt;avalonDock:LayoutDocumentPane&gt;
                            &lt;avalonDock:LayoutDocument Title="Doc1"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutDocument&gt;
                            &lt;avalonDock:LayoutDocument Title="Doc2"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutDocument&gt;
                        &lt;/avalonDock:LayoutDocumentPane&gt;
                        &lt;avalonDock:LayoutDocumentPane&gt;
                            &lt;avalonDock:LayoutDocument Title="Doc3"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutDocument&gt;
                        &lt;/avalonDock:LayoutDocumentPane&gt;                        
                    &lt;/avalonDock:LayoutDocumentPaneGroup&gt;
                    &lt;avalonDock:LayoutAnchorablePaneGroup DockWidth="150" Orientation="Vertical"&gt;
                        &lt;avalonDock:LayoutAnchorablePane&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Tool 1"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Tool 2"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                        &lt;/avalonDock:LayoutAnchorablePane&gt;
                        &lt;avalonDock:LayoutAnchorablePane&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Tool 3"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Tool 4"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                        &lt;/avalonDock:LayoutAnchorablePane&gt;
                    &lt;/avalonDock:LayoutAnchorablePaneGroup&gt;
                &lt;/avalonDock:LayoutPanel&gt;
                &lt;avalonDock:LayoutRoot.LeftSide&gt;
                    &lt;avalonDock:LayoutAnchorSide&gt;
                        &lt;avalonDock:LayoutAnchorGroup&gt;
                            &lt;avalonDock:LayoutAnchorable Title="Autohidden Content"&gt;
                                &lt;TextBox/&gt;
                            &lt;/avalonDock:LayoutAnchorable&gt;
                        &lt;/avalonDock:LayoutAnchorGroup&gt;
                    &lt;/avalonDock:LayoutAnchorSide&gt;
                &lt;/avalonDock:LayoutRoot.LeftSide&gt;
            &lt;/avalonDock:LayoutRoot&gt;
        &lt;/avalonDock:DockingManager&gt;

    &lt;/Grid&gt;
&lt;/Window&gt;</pre> 

运行这个工程你就可以重新排列内容了。移动他们到浮动窗口。为了更加熟悉AvalonDock，我建议你多试几次，然后重新排列内容实现更复杂的布局。

在入门指南的第二部分。我将演示我们如何保存布局。附加事件像DocumentClose/ DocumentClosing 或者ActiveContent 改变的事件。
原文地址：[AvalonDock 2.0 getting started guide PART 1](http://avalondock.codeplex.com/wikipage?title=AvalonDock%202.0%20Getting%20Start%20Guide&referringTitle=Documentation "AvalonDock%202.0%20Getting%20Start%20Guide&referringTitle=Documentation")
著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！