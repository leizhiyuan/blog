title: "图片压缩工具源码（C#）"
tags:
  - C＃
  - 总结
id: 474
categories:
  - 学习笔记
date: 2012-05-04 08:17:30
---

以前在用一个破解版的图片压缩工具。今天早上想想自己也试着做一个吧。查了一些资料。参考了一些代码。总算是写出来了。其实关键代码很简单。主要还是画界面，处理边界。。
看下效果图
[![]({{BASE_PATH}}/images/00f5221099dd3a813d09fbb95fe9a36e5268d652.jpg)](http://leaverimage.b0.upaiyun.com/21131_o.jpg)
处理图像的关键代码是这样的：
<pre lang="java"> 
    bmp = new Bitmap(width, height);  //创建一张空白画布
    grap = Graphics.FromImage(bmp);  //以该画布创建一个绘图对象
 //平滑的高品质，抗锯齿
    grap.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.HighQuality; 
 //HighQualityBicubic 是质量最好的绘图模式
    grap.InterpolationMode = System.Drawing.Drawing2D.InterpolationMode.HighQualityBicubic;
    grap.DrawImage(img, new Rectangle(0, 0, width, height)); //用选中的原图在画布上绘图
    bmp.Save(topath, System.Drawing.Imaging.ImageFormat.Jpeg);  //对图像压缩后保存
</pre>

另外一个就是空间的tip功能，vs2010中貌似已经不支持直接使用属性来设置tip了。所以用到了ToolTip类，使用方法如下，你也可以先导入库，然后简写，ToolTip对象可以给窗体的控件设置提示。主要是第二句话，第一个参数是控件名，第二个参数是提示文本，最好将这两句放入窗体的Load方法中
<pre lang="java">
ToolTip toolTip = new System.Windows.Forms.ToolTip();
toolTip.SetToolTip(this.lboxPicPath, "双击可删除不需要的图片");
</pre>
[![]({{BASE_PATH}}/images/516b951c5df12c7569b3cfefd817a894d380c063.jpg)](http://leaverimage.b0.upaiyun.com/21132_o.jpg)

还有一些边界，比如只接受数字数字之类的，我在代码里给出了详细的说明。实用工具的效果自行测试。对大图片效果最好。

源码下载：[图片压缩工具源码](http://pan.baidu.com/share/link?shareid=62101&uk=1493685990)