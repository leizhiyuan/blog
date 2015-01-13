title: Mysql ERROR 1064 (42000) 问题
tags:
  - mysql
id: 350
categories:
  - 学习笔记
date: 2012-04-21 11:59:12
---

昨天在lamp环境下写sql语句的时候，其中有一条语句是这样的，看着没什么错啊。。然后执行会报ERROR 1064这个错误。
<pre lang="php">create table book
(id unsigned primary key  auto_increment);
</pre>
后来解决了原来是是类型不全。必须写成
<pre lang="php">
create table book
(id int unsigned primary key auto_increment);</pre>

很诡异。。因为理论上unsigned应该是被识别成int unsigned。。还有其他一些情况，反正就是只要报1064.可能语法错误。可能是拼写。或者是分号，mysql的错误提示是很差的。如果把 auto_increment 自增属性加到非主键上。也会报1064的诡异错误。。

参考：[stackoverflow](http://stackoverflow.com/questions/2758649/unable-create-a-table-error-1064-42000-you-have-an-error-in-your-sql-syntax)