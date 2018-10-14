---
title: "C#删除文件和文件夹到回收站"
tags:
  - C＃
  - windows
  - 大学
  - 开发
  - 编程
id: 1575
categories:
  - 学习笔记
date: 2012-09-24 08:04:45
---

 如果使用C#代码来删除文件或是文件夹。会将文件和文件夹直接删除，而不是删除到回收站。可以调用Microsoft.VisualBasic.dll提供的方法。

首先对项目添加名为Microsoft.VisualBasic.dll的引用，然后添加命名空间

<pre class="lang:default decode:true " >using Microsoft.VisualBasic.FileIO;</pre> 

最后示例代码如下：

<pre class="lang:c# decode:true " >using System;
using Microsoft.VisualBasic.FileIO;
namespace leaver
{
    class Program
    {

        static void Main(string[] args)
        {

            Console.WriteLine("删除文件到回收站");
            string filepath = "leaver.txt";
            FileSystem.DeleteFile(filepath, UIOption.OnlyErrorDialogs, RecycleOption.SendToRecycleBin);
            Console.WriteLine("删除文件完成");

            Console.WriteLine("删除文件夹到回收站");
            string dirpath = "leaver";
            FileSystem.DeleteDirectory(dirpath, UIOption.OnlyErrorDialogs, RecycleOption.SendToRecycleBin);
            Console.WriteLine("删除文件夹完成");
        }
    }
}</pre> 

很简单。。就不多说了。。