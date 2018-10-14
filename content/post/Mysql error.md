---
title: "MySQL ERROR 1005: Can't create table  (errno: 150)解决办法"
tags:
  - mysql
  - 总结
  - 软件
id: 890
categories:
  - 学习笔记
date: 2012-06-10 20:02:31
---

最近在做数据库大作业，采用mysql建立数据库的时候出现了这个情况，查了一下，解决了。

出现问题的大致可能情况

1、外键的引用类型不一样，如主键是int外键是char

2、找不到主表中引用的列

3、主键和外键的字符编码不一致，也可能存储引擎不一样

对于第一个问题，检查一下自己的主外键记录数据类型是否一样，改了就行了，对于第二个问题，同样的道理，确定你主表中有对应的列。对于第三个问题
<pre class="lang:pgsql decode:true  crayon-selected">create table pw_test(
uid int unsigned not null,
primary key (uid),
foreign key (uid) references pw_other(uid)
on delete cascade
on update cascade
)ENGINE = MYISAM;</pre>
&nbsp;

括号外面的语句设置了引擎。实战过程中通过。中间的外键设置了delete 和update约束。uid引用了pw_other表中的uid键
记下语法，出现问题的时候就可以用了。