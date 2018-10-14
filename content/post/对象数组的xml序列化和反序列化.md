---
title: 对象数组的Xml序列化和反序列化
tags:
  - C＃
  - 原创
  - 开发
  - 总结
  - 编程
  - 软件
id: 645
categories:
  - 学习笔记
date: 2012-05-18 09:38:27
---

使用Serialization来进行序列化和反序列化，因此需要引入System.Xml.Serialization；命名空间。

为什么要做序列化和反序列化？
.Net程序执行时，对象都驻留在内存中；内存中的对象如果需要传递给其他系统使用；或者在关机时需要保存下来以便下次再次启动程序使用就需要序列化和反序列化。

本文的原始例子在参考文中，但是参考文中没有给出反序列化的例子，且xml文件不清晰。于是修改了代码，同时实现对象数组序列化和反序列化。

<pre lang="java">
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Xml.Serialization;
using System.IO;

namespace UseXmlSerialization
{
    class Program
    {
        static void Main(string[] args)
        {
            //声明一个猫对象
            var cWhite = new Cat { Color = "White", Speed = 10, Saying = "I am a good WHITE cat" };
            var cBlack = new Cat { Color = "Black", Speed = 10, Saying = "I am a good BLACK cat" };

            CatCollection cc = new CatCollection { Cats = new Cat[] { cWhite, cBlack } };

            //序列化这个对象
            XmlSerializer serializer = new XmlSerializer(typeof(CatCollection));

            StringWriter sw = new StringWriter();
            serializer.Serialize(sw, cc);  //序列化到StringWriter对象sw里
            string strXml = sw.ToString();
            Console.WriteLine("对象数组序列化后：");
            Console.WriteLine(strXml);
            Console.WriteLine("xml反序列化后：");
            StringReader sr = new StringReader(strXml);
            CatCollection c2 = serializer.Deserialize(sr) as CatCollection;
            for (int i = 0; i < 2; i++)  //验证xml文件是否已经被反序列化为对象数组
            {
                Console.WriteLine("I am Cat " + i);
                Console.WriteLine(c2.Cats[i].Color);
                Console.WriteLine(c2.Cats[i].Saying);
                Console.WriteLine();

            }
        }
    }
    //以下为类定义
    [XmlRoot("Cats")]
    public class CatCollection
    {
        [XmlElement("Cat")]
        public Cat[] Cats { get; set; }
    }

    public class Cat
    {
        //定义Color属性的序列化为cat节点的属性
        [XmlAttribute("color")]
        public string Color { get; set; }

        //要求不序列化Speed属性
        [XmlIgnore]
        public int Speed { get; set; }

        //设置Saying属性序列化为Xml子元素
        [XmlElement("saying")]
        public string Saying { get; set; }
    }
}
</pre>

参考：
　　[http://www.cnblogs.com/yukaizhao/archive/2011/07/22/xml-serialization.html](http://www.cnblogs.com/yukaizhao/archive/2011/07/22/xml-serialization.html)