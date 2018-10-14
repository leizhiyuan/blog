---
title: "C#中XML和二进制的序列化"
tags:
  - C＃
  - 原创
  - 大学
  - 总结
  - 软件
id: 1154
categories:
  - 学习笔记
date: 2012-07-07 18:30:23
---

看书的时候，看到的。然后感觉书上的写的不清楚，于是自己写了一下。还真的有问题。
要进行序列化和反序列化，首先要定义一个可以序列化的类，方法是在类的声明前加上特性
[Serializable]
定义了一个简单的用户类，需要注意的是私有字段是不能序列化的，只有公有字段和公有属性才可以。如下

<pre class="lang:c# decode:true " >[Serializable]  //特性，可以序列化
    public class UserData
    {
        public string Name;
        public int Age;
        private string sex;
        public string Sex
        {
            set{sex=value;}
            get{return sex;}
        }
        public UserData() 
        { 
        }
        public UserData(string name, int age, string sex)
        {
            Name = name;
            Age = age;
            Sex = sex;
        }
    }</pre> 
注意，书上有个例子没有给出默认的构造函数，实际测试时如果没有默认构造函数，是不能执行xml序列化的。注意。

然后就导入需要的命名空间。
需要导入

<pre class="lang:c# decode:true " >using System.IO;
using System.Runtime.Serialization.Formatters.Binary;
using System.Xml.Serialization;</pre> 
分贝对应文件操作，二进制序列化和xml序列化

为了方便，我封装了四个静态函数，用于实现序列化和反序列化。

<pre class="lang:c# decode:true " > //封装二进制序列化方法
        public static void BinarySerialize(UserData user)
        {
            FileStream fs = new FileStream("user.bin", FileMode.Create);
            BinaryFormatter formater = new BinaryFormatter();
            //执行序列化
            formater.Serialize(fs, user);
            fs.Close();
        }

        //封装二进制反序列化方法
        public static UserData BinaryDeserialize()
        {
            FileStream fs = new FileStream("user.bin", FileMode.Open, FileAccess.Read, FileShare.Read);
            BinaryFormatter formater = new BinaryFormatter();
            UserData user = formater.Deserialize(fs) as UserData;
            fs.Close();
            return user;
        }

        //封装xml序列化方法
        public static void XmlSerialize(UserData user)
        {
            XmlSerializer serializer = new XmlSerializer(typeof(UserData));
            FileStream fs = new FileStream("user.xml", FileMode.Create);
            serializer.Serialize(fs, user);
            fs.Close();
        }

        //封装xml反序列化方法
        public static UserData XmlDeserialize()
        {
             XmlSerializer serializer = new XmlSerializer(typeof(UserData));
            FileStream fs = new FileStream("user.xml", FileMode.Open);
            UserData user = serializer.Deserialize(fs) as UserData;
            fs.Close();
            return user;
        }</pre> 
代码比较简单。
 最后是一个简单的测试。
<pre class="lang:c# decode:true " >static void Main(string[] args)
        {
            UserData user = new UserData("bystander", 20, "man");
            UserData userAfterBin;
            UserData userAfterXml;
            //二进制序列化测试
            BinarySerialize(user);
            //二进制反序列化
            userAfterBin = BinaryDeserialize();
            Console.WriteLine("二进制反序列化测试");
            Console.WriteLine("Name:{0}\nAge:{1}\nSex:{2}", userAfterBin.Name, userAfterBin.Age, userAfterBin.Sex);
            //xml序列化测试
            XmlSerialize(user);
            //xml反序列化测试
            userAfterXml = XmlDeserialize();
            Console.WriteLine("XML反序列化测试");
            Console.WriteLine("Name:{0}\nAge:{1}\nSex:{2}", userAfterXml.Name, userAfterXml.Age, userAfterXml.Sex);

        }</pre> 