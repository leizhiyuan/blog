title: Html解析工具-HtmlAgilityPack
tags:
  - C＃
  - 原创
  - 大学
  - 学习
  - 软件
id: 1531
categories:
  - 学习笔记
date: 2012-09-22 08:00:14
---

这个工具是在暑假的时候发现的。但是最后没用这个工具。不过，这个工具可是非常强悍的。。

[HtmlAgilityPack](http://htmlagilitypack.codeplex.com)主要就是解析DOM的。常用的基础类其实不多，对解析DOM来说，就只有HtmlDocument和HtmlNode这两个常用的类，还有一个 HtmlNodeCollection集合类。我给出一个抓取我博客首页文章的例子。看代码可能更清楚一点。你可以去看看压缩包里提供的文档。

xpath如果自己写表达式比较麻烦。所以我还找到了这个HtmlAgilityPack提供了的一个xpath辅助工具-HAPExplorer。都给出了地址。

首先看我的例子，抓取我博客的首页文章：

<pre class="lang:c# decode:true " >using System;
using System.Collections.Generic;
using System.Text;
using System.Threading;
using System.Diagnostics;
using System.Threading.Tasks;
using System.IO;
using System.Data;
using System.Data.SqlClient;
using HtmlAgilityPack;
namespace leaver
{
    class Program
    {

        static void Main(string[] args)
        {
            HtmlWeb web = new HtmlWeb();
            HtmlDocument doc = web.Load("http://leaver.me/");

            HtmlNode node = doc.GetElementbyId("content");
            StreamWriter sw = File.CreateText("leaver.txt");
            //从根节点选中class=hfeed的节点
            string cfeed = node.SelectSingleNode("/html[1]/body[1]/div[1]/div[1]/div[2]/div[1]/div[1]").OuterHtml;

            HtmlNode hfeed = HtmlNode.CreateNode(cfeed);
            foreach (HtmlNode child in hfeed.ChildNodes)
            {
                if (child.Attributes["id"] == null || child.Attributes["id"].Value.Substring(0, 2) != "po")
                    continue;
                HtmlNode hn = HtmlNode.CreateNode(child.OuterHtml);

                Write(sw, String.Format("标题：{0}", hn.SelectSingleNode("//*[@class=\"entry-title\"]").InnerText));
                Write(sw, String.Format("日期：{0}", hn.SelectSingleNode("//*[@class=\"byline\"]").InnerText));
                Write(sw, String.Format("摘要：{0}", hn.SelectSingleNode("//*[@class=\"entry-summary\"]").InnerText));
                Write(sw, "----------------------------------------");

            }
            sw.Close();
            Console.ReadLine();
        }

        static void Write(StreamWriter writer, string str)
        {
            Console.WriteLine(str);
            writer.WriteLine(str);
        }
    }
}</pre> 

程序运行结果：
[![](/images/d2623dbdf85b9caabedb4336e53e1fd67cfc07d6.jpg "result")](http://leaverimage.b0.upaiyun.com/27392_o.jpg)
xpath表达式的具体书写都是需要分析你需要解析的网站源码的。。。。
xpath辅助工具的界面：
[![](/images/2387485c54eef302fae5ddba1382b6a2092c4b67.jpg "xpath")](http://leaverimage.b0.upaiyun.com/27391_o.jpg)
下载：[HtmlAgilityPack](http://pan.baidu.com/share/link?shareid=61130&uk=1493685990)
下载：[HAPExplorer](http://pan.baidu.com/share/link?shareid=61131&uk=1493685990)