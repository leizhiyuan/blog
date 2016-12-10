title: graylog中的mongodb配置
date: 2016-11-06 14:40:03
tags:
categories:
- 学习笔记
---

接手的一个工具平台,发现 graylog 集群使用了单个的 mongodb 作为数据库,于是需要配置一下集群,来防止数据丢失,毕竟很多配置都在里面.

为了以防万一,先备份一下 graylog 的配置.
`mongodump -h dbhost -d dbname -o dbdirectory`

防止分布式部署的使用搞坏了.之后的恢复可以使用

`mongorestore -h dbhost -d dbname --directoryperdb dbdirectory` 

来恢复.相关说明可以参考[这里](http://www.runoob.com/mongodb/mongodb-mongodump-mongorestore.html)

之后就可以正式开始了

1. 修改集群名字

   在/etc/mongod.conf 中,修改这个值.设置集群使用的集群名称是 graylog,几个机器都配置一下.都先不要启动

   replication:
     replSetName: graylog

2.  然后添加集群配置

   启动其中一台,然后通过mongo 命令连接上数据库,依次执行下面的命令.注意,这里有个坑.添加本机的时候,一定要写对外的域名或者 ip.否则会导致无法选主.
   ````
	rs.initiate()
	rs.add("<hostname>:27017")
	rs.add("<hostname>:27017")
	rs.add("<hostname>:27017")
	rs.conf()
   ````

3. 开始启动
	
	这里启动就不用说了. `service mongod start` 启动就好了.
   
4. 配置 graylog 集群连接地址

	在/etc/graylog/server/server.conf 中配置.mongodb_uri = mongodb://host1,host2,host3/graylog

后面这个 graylog 就是给 graylog 使用的库名,你可以先创建.


之后mongodb 就开始自行同步了.


[参考]

[高可用的MongoDB集群](http://www.jianshu.com/p/2825a66d6aed)

   ​