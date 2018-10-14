---
title: 执行简单sql的小工具
date: 2015-07-12 19:36:22
tags:
 - 学习
 - 工具
categories: 学习笔记
---

工作过程中,有时候需要在本地执行一些简单的sql,但是不想下载太大的mysql这类客户端.恰好看到[https://code.google.com/p/java-ascii-table/](https://code.google.com/p/java-ascii-table/ "java-ascii-table"),完美辅助,于是写个了简单的工具.应该是支持sqlserver,oracle,和mysql的.mysql的测试了.其他的没有测试.还要继续完善.已经放在了[github](https://github.com/leizhiyuan/sqlclient "sqlclient@github")上.


代码很简单.就不贴了.

## 使用说明
 先打包,然后`https://github.com/leizhiyuan/sqlclient/blob/master/README.md` 根据不同的情况写几个简单的bat就可以了.

1. mysql
 `java -jar sqlclient.jar -u "jdbc:mysql://localhost:3306/mysql" -n "name"` 
  `-p "pass" -d "com.mysql.jdbc.Driver"`
2. oracle
 `java -jar sqlclient.jar -u "jdbc:oracle:thin:@127.0.0.1:1521:XE" -n "name"`
 `-p "pass" -d "oracle.jdbc.driver.OracleDriver"`
3. sqlserver
 `java -jar sqlclient.jar -u "jdbc:jtds:sqlserver://localhost:1433/sqlserver"`
 `-n "name" -p "pass" -d "net.sourceforge.jtds.jdbc.Driver"`


## 截图
交互式执行截图
![交互式截图](/images/sqlclient-interactive-screen.jpg)

普通执行截图
![一次执行](/images/sqlclient-direct-sql-screen.jpg)


## 引用
1. [java-ascii-table项目](https://code.google.com/p/java-ascii-table/ "java-ascii-table")
2. [commons-cli命令行解析](https://commons.apache.org/proper/commons-cli/ "commons-cli")

