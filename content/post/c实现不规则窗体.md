---
title: "C#WinForm实现不规则窗体"
tags:
  - 原创
  - 大学
  - 学习
  - 总结
  - 笔记
  - 软件
id: 990
categories:
  - 学习笔记
date: 2012-06-21 09:26:34
---

这个纯属娱乐，因为其实用的不是太多，因为非主流，非标准的界面不符合用户的体验，不符合可用性功能的某一条HE规则。

为了完成这个效果，首先需要自己动手画个你需要的界面出来，界面边缘需要是一种可以很好区别的颜色，比如纯蓝色，因为实现不规则窗体是让C#使边缘颜色透明化来实现的，所以需要唯一识别。因为我用的图是一张灰色的图，我然后圈了一个蓝色的边缘。

刚开始的图；

[![](/images/80a3c49a3718c6f180278a86ba73b2a3d0d39966.jpg "form")](http://leaverimage.b0.upaiyun.com/23583_o.jpg)

然后新建windows应用程序。创建windows窗体并设置窗体基本属性。
（1）将 FormBorderStyle 属性设置为 None。
（2）将窗体的 BackgroundImage 属性设置为先前创建的位图文件。不必将文件添加到项目系统中；这将在指定该文件作为背景图像时自动完成。
（3）将 TransparencyKey 属性设置为位图文件的背景色，本例中为蓝色。（此属性告诉应用程序窗体中的哪些部分需要设置为透明。 ）
上面两个步骤已经完成了不规则窗体自身显示效果的制作。

有人说在24位色以下的环境中可以显示正常，但在24位色以上时黄色背景不能消失，所以上述不能胜任24位色以上环境。但我看到了一种解决方法，那就是先将背景图片添加到资源文件，然后在窗体构造时为窗体设置背景图片：

&nbsp;
<pre class="lang:c# decode:true">private void Form1_Load(object sender, EventArgs e){

   Bitmap bmp = Properties.Resources.form2;
   bmp.MakeTransparent(Color.Blue);
   // bmp.MakeTransparent(Color.FromArgb(2,2,2));如果rgb则是这样用
   this.BackColor = Color.Blue;
   this.BackgroundImage = bmp;
   this.TransparencyKey = Color.Blue;

}</pre>
&nbsp;

实测是可以的。

然后<span style="font-size: medium;"><span style="font-size: medium;">就是为窗体添加移动、关闭、最大最小化的事件。代码直接给出</span></span>
<pre class="lang:c# decode:true">private bool isMouseDown = false;  //记录鼠标是否被按下
private Point position;  //记录鼠标位置

private void Form1_MouseDown(object sender, MouseEventArgs e)
{
 if (e.Button == MouseButtons.Left)
 {
    int x = -e.X;
   int y = -e.Y;
   position = new Point(x, y);
   isMouseDown = true;
 }

}

private void Form1_MouseMove(object sender, MouseEventArgs e)
{
 if (isMouseDown)
 {
   Point newPosition = Control.MousePosition;
   newPosition.Offset(position);
   this.Location = newPosition;
 }
}

private void Form1_MouseUp(object sender, MouseEventArgs e)
{
 if (e.Button == MouseButtons.Left)
 {
    isMouseDown = false;
 }
}</pre>
&nbsp;

还有其他一些比如关闭按钮的添加，都很简单，直接添加一个button，事件里写，两个选一个。
<pre class="lang:c# decode:true">this.Close();//关闭此窗体
Application.Exit();//退出应用程序</pre>
我最终的效果是个圆，可以看到，锯齿很明显，我想要效果好的话，那个位图得好好设计。这个只是演示。。所以。。还有一种方法是链接1中提供的，有兴趣的可以试试。

[![](/images/33eb616a6ae722d472de3408f8a03596aaf5baaf.jpg "demo")](http://leaverimage.b0.upaiyun.com/23584_o.jpg)

工程源码下载：[IrregularForm.7z](http://115.com/file/c2ai3t6p#IrregularForm.7z)

参考：

[http://www.cnblogs.com/KissKnife/archive/2006/10/02/520116.html](http://www.cnblogs.com/KissKnife/archive/2006/10/02/520116.html)

[http://allancandy.cnblogs.com/archive/2005/09/01/227814.html](http://allancandy.cnblogs.com/archive/2005/09/01/227814.html)