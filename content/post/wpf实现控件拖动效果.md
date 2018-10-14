---
title: WPF实现控件拖动效果
tags:
  - C＃
  - 大学
  - 编程
  - 设计
  - 软件
id: 1314
categories:
  - 学习笔记
date: 2012-07-27 07:18:40
---

首先很简单，当然是去添加两个控件了，这里我添加了两个控件，都是label，然后我想实现的是将label1拖动到label2上的时候，label1的内容会被复制到label2上。

<pre class="lang:c# decode:true " > &lt;Label Content="TestDrop" 
Height ="28" HorizontalAlignment="Left" Margin="70,35,0,0" Name ="label1" 
VerticalAlignment="Top" MouseDown="label1_MouseDown"  /&gt;

&lt;Label Content ="ToHere" Height="28" HorizontalAlignment="Left" 
Margin ="342,107,0,0" Name="label2" VerticalAlignment="Top" AllowDrop ="True"
 Drop="tagert_drop"   /&gt;</pre> 

需要注意的代码是label1中的MouseDown事件。和label2中的AllowDrop ="True"  Drop="tagert_drop"

然后对应的处理事件

<pre class="lang:c# decode:true " >     private void label1_MouseDown(object sender, MouseButtonEventArgs e)
        {
            Label lbl = (Label )sender;
            DragDrop.DoDragDrop(lbl, lbl.Content, DragDropEffects .Copy);
        }

        private void tagert_drop(object sender, DragEventArgs e)
        {
            (( Label)sender).Content = e.Data.GetData(DataFormats.Text);
        }</pre> 

 其他的效果可以仿照这个来做。比如拖动以后的效果可以DragDropEffects来设置。。