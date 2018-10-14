---
title: "C#使用Graphics创建饼图"
tags:
  - C＃
  - windows
  - 大学
  - 翻译
id: 1703
categories:
  - 我的翻译
date: 2012-09-27 13:47:25
---

### 介绍

这个程序是使用C#中的Graphics来创建一个饼图的，我已经尽我所能写的很好了。如果你有任何建议可以分享给我，这样我也能从中学习。

### 使用代码

最近我迷上了Graphics类。我仅仅体验了一下Graphics的DrawPie() 和FillPie() 方法。

最为一个简单的Demo，我创建一个有着五个文本框的窗体，一个按钮，一个图片框。一会我就把饼图画在图片框里

[![](/images/f3681994ca4fce2b724886db9d7a958d854c8e39.png "client")](http://leaverimage.b0.upaiyun.com/27557_o.png)
在创建一个饼图之前，我们头脑里要有这个意识。我们不能创建一个不符合常规的圆，创建圆我们需要度数信息。

为了转换度数。我们首先把给定的值做个求和。然后得出文本框里所有值的和。然后呢。用每个值除以总值再乘以360度。
代码如下：
<pre class="lang:c# decode:true " >int i1 = Int32.Parse(textBox1.Text);
int i2 = Int32.Parse(textBox2.Text);
int i3 = Int32.Parse(textBox3.Text);
int i4 = Int32.Parse(textBox4.Text);
int i5 = Int32.Parse(textBox5.Text);

float total = i1 + i2 + i3 + i4 + i5 ;

float deg1 = (i1 / total) * 360;
float deg2 = (i2 / total) * 360;
float deg3 = (i3 / total) * 360;
float deg4 = (i4 / total) * 360;
float deg5 = (i5 / total) * 360;</pre>
值转换完毕后。我们可以创建Graphics类的实例了。

<pre class="lang:c# decode:true " >Graphics graphics = pictureBox1.CreateGraphics();  </pre>

然后我们需要创建一个矩形区域，在这个矩形区域里绘制饼图。
<pre class="lang:c# decode:true " >Rectangle rect = new Rectangle(0, 0, 150, 150);</pre>
前两个参数定义了矩形左上角的坐标，后两个分别定义了举行的宽和高。

为了能够比较清晰的看出饼图各部分的比例。我们需要创建五个笔刷。
<pre class="lang:c# decode:true " >Brush brush1 = new SolidBrush(Color.Red);
Brush brush2 = new SolidBrush(Color.Blue);
Brush brush3 = new SolidBrush(Color.Maroon);
Brush brush4 = new SolidBrush(Color.Navy);
Brush brush5 = new SolidBrush(Color.YellowGreen);
graphics.Clear(pictureBox1.BackColor);</pre>

现在我们可以开始创建我们的饼图了。graphics.FillPie();方法接受四个参数

1.	笔刷，用来填充扇形
2.	矩形：饼图将被创建的区域。
3.	开始角度：饼部分的开始角度
4.	覆盖角度：饼部分扫过的角度

一般来说graphics.FillPie();方法并不是创建一个完全的饼图，而是创建饼图的一个扇形部分，我们会创建一系列的扇形最终看起来像是一个饼图。

<pre class="lang:c# decode:true " >graphics.FillPie(brush1, rect, 0, deg1);
graphics.FillPie(brush2, rect, deg1, deg2);
graphics.FillPie(brush3, rect, deg1 + deg2, deg3);
graphics.FillPie(brush4, rect, deg1 + deg2 + deg3, deg4);
graphics.FillPie(brush5, rect, deg1 + deg2 + deg3 + deg4, deg5);</pre> 

上面代码的第一行将会创建第一个红色的扇形。起点是0度，并且创建一个计算好的扇面。
之后，在第二行，我们的扇面的起点就会接着上一步的结束的位置，也就是deg1 ，第二行，我们的起点就会是 deg1+deg2

这段代码如你所见，可能不是最优的。我们创建了五个不同的笔刷。和不同的浮点数，而这些操作可以用循环完成。。

我故意没有这样做。因为，这样做了就可能使问题复杂化。下面是使用循环和数组完成相同的工作。

<pre class="lang:c# decode:true " >private void createPie()
{
   iarrPieValues = new int[lstValuesCount];

   for (int iCnt = 0; iCnt < lstValuesCount; iCnt++)
   {
       iarrPieValues[iCnt] = Int32.Parse(lstValues.Items[iCnt].ToString());
       sum += iarrPieValues[iCnt];
   }

   Color[] color = { Color.Red, Color.Blue, Color.Maroon, Color.Yellow, Color.Green, Color.Indigo };
   Rectangle rect = new Rectangle(30, 10, 130, 130);

   Graphics graphics = pictureBox1.CreateGraphics();
   graphics.Clear(pictureBox1.BackColor);

   float fDegValue = 0.0f;
   float fDegSum = 0.0f;

   for (int iCnt = 0; iCnt < iarrPieValues.Length; iCnt++)
   {
       fDegValue = (iarrPieValues[iCnt] / sum) * 360;
       Brush brush = new SolidBrush(color[iCnt]);
       graphics.FillPie(brush, rect, fDegSum, fDegValue);
       fDegSum += fDegValue;
   }
}</pre>

[![](/images/341f6010ffe346f8245bdae48986ae61c6dfe90f.png "listbox")](http://leaverimage.b0.upaiyun.com/27558_o.png)
首先我创建一个整型数组，数组元素个数等于列表框里的数目。然后我使用一个循环计算了所有值的和。
然后。我创建了一个颜色的数组，这里我建议不要创建一个多于6个扇形。。不然看起来就连在一起了。。
第三步呢。。我再次使用循环去把值转换成度数。fDegValue 用来保存每次计算的度数。在我们前面的例子里。我们需要把前面几个值加起来作为下一个扇形的开始。而这里。我们使用fDegSum 来保存前面的和就很好了。

本文很简单，也就使用Graphics创建一个饼图而已。如果是要创建一些专业的图表就要用到.net提供的图表控件了。

下载：[源代码和Demo](http://pan.baidu.com/share/link?shareid=63681&uk=1493685990)

原文地址：[Create-Pie-Chart-Using-Graphics-in-Csharp-NET](http://www.codeproject.com/Articles/463284/Create-Pie-Chart-Using-Graphics-in-Csharp-NET)
著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！