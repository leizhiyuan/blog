---
title: WPF毛玻璃效果Demo和一个问题
tags:
  - C＃
  - WPF
  - 原创
  - 大学
  - 开发
  - 总结
  - 收藏
id: 1579
categories:
  - 学习笔记
date: 2012-09-24 08:04:25
---

　　那天看到WPF书上讲的毛玻璃效果，就去找了下效果。。忘了例子是从哪发现得了。。先看下效果，

[![](/images/638a69761205e3d5c16a5bd34a2f775de190d137.jpg "glass")](http://leaverimage.b0.upaiyun.com/27420_o.jpg)
　　但是这不是重点，作者给出的代码有一个设计时错误。。错误提示为：
无法将类型为“Microsoft.Expression.Platform.WPF.InstanceBuilders.WindowInstance”的对象强制转换为类型“System.Windows.Window”，，

　　中文搜了一下。没有发现有人解决过。目测。。。然后又拿英文搜了下。几经辗转。。终于是解决了。。原文在[Unable to cast XAML error](http://social.msdn.microsoft.com/Forums/is/wpf/thread/931e75a8-cab6-492d-89cd-b7ca291fa273)。其实就是将原作者这个函数修改如下的

<pre class="lang:c# decode:true " >public static void OnIsEnabledChanged(DependencyObject obj, DependencyPropertyChangedEventArgs args)
        {

            if ((bool)args.NewValue == true)
            {
                Window wnd = obj as Window;
                if (wnd != null) wnd.Loaded += new RoutedEventHandler(wnd_Loaded);
            }
        }</pre> 
　　也就是验证了一下转换是否成功。

下载：[修改后的RGSamples](http://pan.baidu.com/share/link?shareid=61615&uk=1493685990)