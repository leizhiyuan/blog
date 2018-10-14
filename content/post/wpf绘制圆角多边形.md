---
title: WPF绘制圆角多边形
tags:
  - C＃
  - WPF
  - 大学
  - 学习
  - 编程
  - 翻译
  - 软件
id: 1996
categories:
  - 我的翻译
date: 2012-10-13 09:45:40
---

**介绍**

最近，我发现我需要个圆角多边形。而且是需要在运行时从用户界面来绘制。WPF有多边形。但是不支持圆角。我搜索了一下。也没找到可行的现成例子。于是就自己做吧。本文描述了圆角多边形的实现，也包括如何用在你的项目里。在Demo里面的RoundedCornersPolygon 类是完整的实现。

下载的Demo包括两部分

1\. 通过XAML绘制圆角多边形

[![](/images/83dd3b37c81d0d4e1a82e80ba69d00411930ce52.png "1")](http://leaverimage.b0.upaiyun.com/28036_o.png)

2\. 运行时创建圆角多边形

[![](/images/864fccf9677ea6ce52dad56c85cf7389108c6874.png "2")](http://leaverimage.b0.upaiyun.com/28037_o.png)

**背景**
多边形可以被认为是沿着一个给定半径的圆的边缘和一些指定点/边。所构成的点的集合。

[![](/images/82969618833250386b07a5177dc8c454da0530e1.gif "3")](http://leaverimage.b0.upaiyun.com/28038_o.gif)
在WPF中。你可以给Polygon对象的Points属性添加一系列的点来制作多边形。

XAML方式
<pre class="lang:default decode:true">&lt;Canvas&gt;
    &lt;Polygon Points="10,50 180,50 180,150 10,150" 
       StrokeThickness="1" Stroke="Black" /&gt;
&lt;/Canvas&gt;</pre>
C#方式
<pre class="lang:default decode:true">var cnv = new Canvas(); 
var polygon = new Polygon {StrokeThickness = 1, Fill = Brushes.Black};
polygon.Points.Add(new Point(10, 50)); 
polygon.Points.Add(new Point(180, 50));
polygon.Points.Add(new Point(180, 150));
polygon.Points.Add(new Point(10, 150));
cnv.Children.Add(polygon);
this.Content = cnv;</pre>
&nbsp;

上面两个例子会输出下面的矩形

[![](/images/787268128d45598cff769b9209e41652a66775ae.png "4")](http://leaverimage.b0.upaiyun.com/28039_o.png)

**使用代码**
我写的RoundedCornersPolygon 类和普通的多边形类很相似。但是有更多的属性来控制圆角。首先。看一个例子。展示一下圆角矩形类的使用

XAML方式
<pre class="lang:default decode:true">&lt;Canvas&gt;
    &lt;CustomRoundedCornersPolygon:RoundedCornersPolygon Points="10,50 180,50 180,150 10,150" 
               StrokeThickness="1" Stroke="Black" ArcRoundness="25" 
               UseAnglePercentage="False" IsClosed="True"/&gt;
&lt;Canvas&gt;</pre>
C#方式
<pre class="lang:default decode:true">var cnv = new Canvas();  
var roundedPolygon = new RoundedCornersPolygon
{
    Stroke = Brushes.Black, StrokeThickness = 1, 
    ArcRoundness = 25, UseAnglePercentage = false, IsClosed = true
};
roundedPolygon.Points.Add(new Point(10, 50));
roundedPolygon.Points.Add(new Point(180, 50));
roundedPolygon.Points.Add(new Point(180, 150));
roundedPolygon.Points.Add(new Point(10, 150));
cnv.Children.Add(roundedPolygon);
this.Content = cnv;</pre>
输出如下：

[![](/images/fb11bced67d29f74ff4e539a8008871934d74c04.png "5")](http://leaverimage.b0.upaiyun.com/28040_o.png)

多边形有四个主要属性
ArcRoundness 属性指定了从距离LineSegment终点多远的距离开始弯曲，通常和UseRoundnessPercentage 一起使用。UseRoundnessPercentage属性指定了ArcRoundness 值是百分比还是一个固定的值。

举个例子。ArcRoundness 被设置成10，而且UseRoundnessPercentage 被设置成false，那么弯曲将会在距离线段终点10的地方开始。而如果UseRoundnessPercentage 被设置成ture。则会是从线段终点10%的地方开始弯曲。
<pre class="lang:default decode:true">/// &lt;summary&gt; 
/// Gets or sets a value that specifies the arc roundness.
/// &lt;/summary&gt;
public double ArcRoundness { get; set; }

/// &lt;summary&gt;  
/// Gets or sets a value that specifies if the ArcRoundness property
/// value will be used as a percentage of the connecting segment or not.
/// &lt;/summary&gt;
public bool UseRoundnessPercentage { get; set; }</pre>
IsClosed 指定多边形的最后一个点是否和第一个点闭合。为了成为一个多边形。一般应该被设置为true
<pre class="lang:default decode:true">/// &lt;summary&gt; 
/// Gets or sets a value that specifies if the polygon will be closed or not.
/// &lt;/summary&gt; 
public bool IsClosed { get; set; }</pre>
&nbsp;

Points属性则代表了多边形点的集合。
<pre class="lang:default decode:true">/// &lt;summary&gt; 
/// Gets or sets a collection that contains the points of the polygon.
/// &lt;/summary&gt;
public PointCollection Points{ get; set; }</pre>
&nbsp;

**如何实现**
控件实现了Shape 类，被用来画多边形的形状是一个Path对象。我们会往Path对象里添加LineSegment 和QuadraticBezierSegment 对象。QuadraticBezierSegment 对象表示一个贝塞尔曲线。由三个点定义。更多的信息请查看

对于一个普通的多边形，只有LineSegment是必须的，但是为了设计圆角多边形。就需要贝塞尔曲线了。每一次一个点被添加/一个属性被修改。形状会重绘。做圆角的关键方式就是ConnectLinePoints 方法。
<pre class="lang:default decode:true">/// &lt;summary&gt;
/// 使用公共点连接两条线段,
/// 通过3个点的指定来绘制弯曲部分
/// &lt;/summary&gt;
/// &lt;param name="pathFigure"&gt;&lt;/param&gt;
/// &lt;param name="p1"&gt;第一条线段的第一个点&lt;/param&gt;
/// &lt;param name="p2"&gt;第二个点，也是公共点&lt;/param&gt;
/// &lt;param name="p3"&gt;第二条线段的第二个点&lt;/param&gt;
/// &lt;param name="roundness"&gt;角的弧度&lt;/param&gt;
/// &lt;param name="usePercentage"&gt;标志值（百分比还是真值） &lt;/param&gt;
private static void ConnectLinePoints(PathFigure pathFigure, Point p1, 
                                      Point p2, Point p3,
                                      double roundness, bool usePercentage)
{
      //第一条线段上弯曲部分开始的位置点.
      Point backPoint;
      //第二条线段上弯曲部分结束的位置点
      Point nextPoint;
      if (usePercentage)
      {
          backPoint = GetPointAtDistancePercent(p1, p2, roundness, false);
          nextPoint = GetPointAtDistancePercent(p2, p3, roundness, true);
      }
      else
      {
          backPoint = GetPointAtDistance(p1, p2, roundness, false);
          nextPoint = GetPointAtDistance(p2, p3, roundness, true);
      }            

      int lastSegmentIndex = pathFigure.Segments.Count - 1;
      //设置第一条线段的终点.
      ((LineSegment)(pathFigure.Segments[lastSegmentIndex])).Point = backPoint;

      //创建并添加弯曲部分
      var curve = new QuadraticBezierSegment { Point1 = p2, Point2 = nextPoint };
      pathFigure.Segments.Add(curve);

      /创建并添加新线段。
      var line = new LineSegment { Point = p3 };
      pathFigure.Segments.Add(line);
}</pre>
&nbsp;

还有两个方法计算弯曲部分开始的点GetPointAtDistance 和GetPointAtDistancePercent，第一个是以值计算，第二个是以百分比计算。
<pre class="lang:default decode:true">/// &lt;summary&gt;
///得到由两个点定义的线段上，给定距离的点
/// &lt;/summary&gt;
/// &lt;param name="p1"&gt;第一条线段的第一个点&lt;/param&gt;
/// &lt;param name="p2"&gt;第一条线段的第二个点&lt;/param&gt;
/// &lt;param name="distance"&gt;点的距离&lt;/param&gt;
/// &lt;param name="firstPoint"&gt;标志值（百分比还是真值）&lt;/param&gt;
/// &lt;returns&gt;计算结果点&lt;/returns&gt;
private static Point GetPointAtDistance(Point p1, Point p2, 
                     double distance, bool firstPoint)
{
     double totalDistance = Math.Sqrt(Math.Pow((p2.X - p1.X), 2) + 
                            Math.Pow((p2.Y - p1.Y), 2));
     double rap = firstPoint ? distance / totalDistance : 
                   (totalDistance - distance) / totalDistance;
     return new Point(p1.X + (rap * (p2.X - p1.X)), p1.Y + (rap * (p2.Y - p1.Y)));
}

private static Point GetPointAtDistancePercent(Point p1, Point p2, 
                     double distancePercent, bool firstPoint)
{
     double rap = firstPoint ? distancePercent / 100 : (100 - distancePercent) / 100;
     return new Point(p1.X + (rap * (p2.X - p1.X)), p1.Y + (rap * (p2.Y - p1.Y)));
}</pre>
&nbsp;

**结论**
依然有很多细节应该完善的。但这仅仅是圆角多边形的一个尝试。比如。在其他情况。每个角应该有不同晚景的圆角，WPF使得一切皆有可能。本文的目的是创建一个大家可以用的圆角多边形，他们可以扩展来满足他们的需求。

**Demo下载**

[downloadicon href=http://pan.baidu.com/share/link?shareid=82217&uk=1493685990]圆角多边形[/downloadicon]

**许可**
本文包括源代码和文件在CPOL下授权。

原文地址：[WPF-rounded-corners-polygon](http://www.codeproject.com/Articles/128705/WPF-rounded-corners-polygon)

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！