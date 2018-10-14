---
title: CSV批量导入解决
tags:
  - C＃
  - 开发
  - 总结
  - 编程
id: 1184
categories:
  - 学习笔记
date: 2012-07-06 18:28:21
---

　　最近的一个事就是那个诡异的CSV导入，后来决定用最笨的方法。就是readline，然后确实是可以正常识别了。思路就是一行行读入，然后构造sql语句，保存为sql文件，然后去批量执行sql文件。其实在codeproject找到了一些类似的工具，都有不如意的地方，但是时间关系还没能自己去定制，这件事完成后，就去完善一下大牛们的工具。

　　因为是多个csv文件，每个文件对应于我们想建的一个表，实际是大约2000个csv文件，每个文件大约4000行数据，构造成对应的sql语句比较简单，需要注意的就是如果sql server表中的字段和关键字重名，那么需要加[]，比如我们有个字段是open，那么实际sql语句中使用的时候，比如

<pre class="lang:pgsql decode:true " >CREATE TABLE 
(
   [open] decimal not null,
)</pre> 

　　简单的读写以后我们构造了2000个sql文件，然后我们需要执行这些sql文件。。必然需要写个代码来批量执行了。。为此，我先用C#生成一个批处理文件，然后来执行

<pre class="lang:c# decode:true " >void GetBat(string InputPath,string OutFile)
        {
            DirectoryInfo di = new DirectoryInfo(InputPath);
            FileInfo[] fi = di.GetFiles();  // Create an array representing the files in the current directory
            StringBuilder strtemp = new StringBuilder();
            foreach (FileInfo fiTemp in fi)
            {
                string temp = "osql -S \"127.0.0.1\"  -U \"sa\" -P \"broker\" -d \"test_money\" -i \"";
                temp += fiTemp.FullName.ToString() + "\"\n";
                strtemp.Append(temp);
            }

            FileStream fs = new FileStream(OutFile, FileMode.OpenOrCreate);
            StreamWriter sw = new StreamWriter(fs);
            sw.WriteLine(strtemp);
            sw.Close();
            fs.Close();
        }</pre> 
　　简单封装了一下，输入参数是文件夹。里面包含我们的所有sql文件。输出为一个批处理文件。。后来。执行下生成的bat文件就可以了。。

<pre class="lang:default decode:true " >osql -S "127.0.0.1"  -U "sa" -P "broker" -d "test_money" -i "D:\test\AMKR.sql"
osql -S "127.0.0.1"  -U "sa" -P "broker" -d "test_money" -i "D:\test\AMLJ.sql"
osql -S "127.0.0.1"  -U "sa" -P "broker" -d "test_money" -i "D:\test\AMLN.sql"
</pre> 

600M的sql文件执行起来三个多小时。。因为只算一次，所以就不考虑优化了。。鄙视我们吧。