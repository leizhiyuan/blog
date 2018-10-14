---
title: zookeeper伪集群部署
date: 2015-11-22 16:06:43
tags: 
 - java 
 - 学习 
 - 并发
categories: 学习笔记
---

zookeeper是用来管理分布式环境的系统主要用来服务发现,配置管理,同步.大致原理是zookeeper 自身集群的每个节点都维护这一个目录树,内容相同,每个节点的数据一致性由zookeeper自身的算法来解决.下篇尝试.zookeeper本篇主要说明如果部署zookeeper的分布式环境.

## 下载 ##
zookeeper由apache在管理,下载地址:[http://www.apache.org/dyn/closer.cgi/zookeeper/](http://www.apache.org/dyn/closer.cgi/zookeeper/).下载完成后,随便放个目录好了..


## 配置 ##

本次创建3个节点.
1 . 存储目录准备
首先给每个伪节点创建一个目录.用来存储每个节点保存的目录信息.真实的分布式环境将对应在不同的机器上.
这里我在D:\zookeeper,创建三个目录,分别是zk1,zk2,zk3.
然后为每个集群编写一个myid文件,标识集群id

2 . 启动配置文件 
下载完成后,在conf目录会看到由一个zoo_sample.cfg实例配置文件,我们可以以这个为模板.来为分布式环境的每个zookeeper节点配置一个节点的数据目录,端口.其他节点的信息等.

我们在conf目录例创建三个配置文件,分别为zk1.cfg,zk2.cfg,zk3.cfg;
里面的值
zk1.cfg
````
tickTime=2000 
initLimit=10 
syncLimit=5 
dataDir=D:/zookeeper/zk1 
clientPort=2181 
server.1=127.0.0.1:2888:3888 
server.2=127.0.0.1:2889:3889 
server.3=127.0.0.1:2890:3890
````

zk2.cfg
````
tickTime=2000 
initLimit=10 
syncLimit=5 
dataDir=D:/zookeeper/zk2 
clientPort=2182 
server.1=127.0.0.1:2888:3888 
server.2=127.0.0.1:2889:3889 
server.3=127.0.0.1:2890:3890
````

zk1.cfg
````
tickTime=2000 
initLimit=10 
syncLimit=5 
dataDir=D:/zookeeper/zk3
clientPort=2183 
server.1=127.0.0.1:2888:3888 
server.2=127.0.0.1:2889:3889 
server.3=127.0.0.1:2890:3890
````

这里的server.1.2.3这就是每个机器对应的myid的值.
server.1=127.0.0.1:2888:3888解释一下这条配置.前面的2888是各个节点用来互相交流.选取leader的端口.后面这个端口,3888是各个节点用来和leader沟通的节点.而clientPort 是开放出去,等待客户端连接的端口.


## 启动 ##

分别启动三个实例,在zookeeper的安装目录下.进如bin目录,复制三个zkServer.cmd 文件,要是linux就不用这么麻烦了..

分别加上一行

set ZOOCFG=../conf/zk1.cfg

最终这个文件像这样
````
setlocal
call "%~dp0zkEnv.cmd"

set ZOOMAIN=org.apache.zookeeper.server.quorum.QuorumPeerMain
set ZOOCFG=../conf/zk1.cfg
echo on
java "-Dzookeeper.log.dir=%ZOO_LOG_DIR%" "-Dzookeeper.root.logger=%ZOO_LOG4J_PROP%" -cp "%CLASSPATH%" %ZOOMAIN% "%ZOOCFG%" %*

endlocal
````
然后直接双击启动zkServer1.cmd,zkServer2.cmd,zkServer3.cmd

刚启动第一个之后,你会看到有报错,是zookeeper进行选举的时候报错的.因为第一个zk节点.从自己的启动配置里,知道还有两个节点,于是尝试连接.但是连接不上,再启动另外两个.都启动后,报错消失

然后在D:\zookeeper中可以看到由数据写入.
![zk数据目录](/images/zk_result.jpg)


## 测试 ##

启动bin目录的zkCli.cmd,自动连接本机的2181端口.也可以自己指定
zkCli.cmd –server 127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183
对客户端来说.连接上了一个列表之后,如果一台挂了,并不会影响.系统依旧可以运行.

然后执行一些简单的操作
![zk测试结果](/images/zk_operation_test.jpg)

显示根目录下、文件： ls / 使用 ls 命令来查看当前 ZooKeeper 中所包含的内容。
显示根目录下、文件： ls2 / 查看当前节点数据并能看到更新次数等数据。
创建文件，并设置初始内容： create /zktest "test" 创建一个新的 znode节点“ zk ”以及与它关联的字符串。
获取文件内容： get /zktest 确认 znode 是否包含我们所创建的字符串。
修改文件内容： set /zktest "zkbak" 对 zk 所关联的字符串进行设置。
删除文件： delete /zktest 将刚才创建的 znode 删除。
退出客户端： quit
帮助命令： help

可以关掉一个服务器,会发现客户端依然正常.可以执行get set等操作.
