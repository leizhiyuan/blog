---
title: "二级域名查找 实现演示（GAPI+Json+C#）"
tags:
  - C＃
id: 341
categories:
  - 学习笔记
date: 2012-04-20 08:23:24
---

　　　　昨天看到了李劼杰的[检索一个域名下属所有子域名的两种方法](http://www.lijiejie.com/index.php/2-ways-to-get-subdomains/)很受启发。想用C#实现一些东西，本来是打算做的相对完整一点的，结果发现Google 开放的api貌似是有限制，段时间内不同提交太多。所以就简单实现一下，有需要的朋友自己扩展

　　　　刚开始的思路是想Google应该是有开放的api吧。。于是先Google C# 收集 Google搜索结果。翻了一会，看到了 [此文](http://zhidao.baidu.com/question/143797250.html) ，一看我去，返回的竟然是json格式。。只听过，从来没有接触过。。。不做总是不会的，试试。

思路；访问该页面，得到字符串-》去掉多余部分-》解析成对象-》提取对象的字段
　　　　1.访问该<pre lang]"C#">http://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=hello</pre>，其实hello部分就是需要查找的字串，对于查找二级域名这种事，应用到Google的一个语法就是site:leaver.me。类似这样的，可以获取到所有leaver.me上的子站和内容。。
然后分析json数据。直接访问会发现格式很乱。使用http://jsonformatter.curiousconcept.com/这个工具进行格式化。最终结果如图
[![](/images/06ed49d2b62047555b3287b29255a3fcaa71d845.jpg)](http://leaverimage.b0.upaiyun.com/20804_z.jpg)
可以很清楚的看出json的结构，results是结果集，我需要的是需要的是[]之间的部分，包括[]，我理解的是这样。默认给出4个结果。

　　　　2.要去掉多余部分，得用正则表达式了，
<pre lang="java"> json = "[" + Regex.Match(json, @"(?<=\[).+?(?=\])", RegexOptions.IgnoreCase).Value + "]";  //得到一个数组[]中间的部分</pre>
json解析我在外国找到了[json.net](https://json.codeplex.com/)这个解析器，导入什么的就不说了，至于用法我是看的他的文档

　　　　3.<pre lang="java">List<Result> results = JsonConvert.DeserializeObject<List<Result>>(json); //list 存放解析的结果，result为对应自写类</pre> 就是将一个json格式的字符串解析成一个list组，组内元素为一个对应的类，就是先分析json的格式。发现每个结果都有如下的格式
<pre lang="java"> 
      public  string GsearchResultClass{ get; set; }
      public string unescapedUrl{ get; set; }
      public string url{ get; set; }
      public string visibleUrl{ get; set; }
      public string cacheUrl { get; set; }
      public string title: { get; set; }
      public string titleNoFormatting { get; set; }
      public string content { get; set; }</pre>
所以写一个类，来用于json数据的反序列号，其实就是解析啦。。这样通过对解析器的调用就能对应着吧json里面的值赋值给类对象了。

　　　　4.完成后就得到了一个List<Result>对象。里面包含四个结果的所有信息，只要调用results[i].对应属性就可以了。这里分析后可知二级域名在visibleUrl里，所有就调用它。用到了HashSet这个模板。元素唯一不重复。

最终效果：
[![](/images/)](http://leaverimage.b0.upaiyun.com/20805_o.jpg)
　　　　写程序需要是处理边界情况。。感觉是。主要是我对json格式的解析很不清楚。。只能边学边做。。还有因为写之前没有规划好，打算写全一些，也没想好每个函数来干什么，结果导致后面很麻烦。竟然用了一个全局变量。。其实可以改的。还是算了，演示而已。而且。当时不太清楚google的限制。到达限制后会返回一个错误的结果，多次异常。不顾加强了调试代码的能力，也好。唉。矬人就是矬人啊。。

源码下载：
[downloadicon href=http://pan.baidu.com/share/link?shareid=91790&uk=1493685990]Finder Demo[/downloadicon]