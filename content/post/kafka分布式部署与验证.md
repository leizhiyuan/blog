---
title: kafka分布式部署与验证
date: 2015-09-05 07:30:13
tags:
 - 中间件
 - 学习
categories: 
  - 学习笔记
---
在[kafka快速上手](http://leaver.me/2015/09/03/kafka%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B/),和[kafka中的partition和offset](http://leaver.me/2015/09/04/kafka%E4%B8%AD%E7%9A%84partition%E5%92%8Coffset/)中,已经解释了kafka的一些原理,和完成了一个简单的生产消费的实践,如第一篇所说,kafka是一个分布式环境下的消息组件,那么,按照我们前面的简单上手,如果kafka的应用进程被杀或者kafka的机器宕机,那么kafka消息组件就无法使用了,或者zookeeper宕机了,那么kafka也无法使用了.

## kafka集群(cluster) ##

一台机器不够,那就多搞几台,首先,启动zookeeper这个就不多说了.可以参看前文,在启动kafka的时候,我们在单机模拟启动多个kafka应用.
首先在config目录,copy两个server.properties 文件,这里我复制三份,分别起名server1.properties ,server2.properties server3.properties
然后修改这三个配置文件,主要修改broker.id=2,port=9094,log.dir=/tmp/kafka-logs-2这三个值,broker.id是用来标记分布式环境中的broker的,要求唯一,port和log.dir一个端口,一个log目录,如果在真实的分布式环境中是不需要修改.这里单机模拟防止端口冲突.

分别把broker.id改为1,2,3,log.dir则分别改成kafka-logs-1,kafka-logs-2,kafka-logs-3,然后依次启动
`kafka-server-start.bat ../../config/server1.properties`
`kafka-server-start.bat ../../config/server2.properties`
`kafka-server-start.bat ../../config/server3.properties`

如果你启动有报错,一个就是之前说的那个vm参数太大,另一个可能是你的端口没改好.具体错误看下报错就好了.

然后我们注册一个topic,叫做replicationtest
`kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic replicationtest`
这里冗余是3,分区是1,那么最终各个broker都会保留一份,最多允许N-1,也就是2台broker宕机,服务照样运行.
注册之后,这时候
`kafka-topics.bat--describe --zookeeper localhost:2181 --topic replicationtest`
执行描述命令,看下集群情况
![集群描述结果](/images/kafka_cluster_show.jpg)
第一行给出了分区的汇总信息。每个分区行给出分区信息。

"Leader" 节点是2.
"Replicas" 信息，在节点2,3,1上,所有的节点信息.
"Isr" 工作中的复制节点的集合. 也就是活的节点的集合.

其他的就不用解释了.这里选出了2是leader,也就是说2这个节点会给消费者提供服务.


然后我们测试一条信息.
`kafka-console-producer.bat --broker-list localhost:7777,localhost:8888,localhost:9999 --topic replicationtest`
上面的7777是server1.properties 中设置的.根据个人情况.改改.然后在控制台发发消息.


然后消费一下.
`kafka-console-consumer.bat --zookeeper localhost:2181 --topic replicationtest`
这里的2181是zookeeper的端口,不用改.
![目前运行结果](/images/kafka_replication_test.jpg)

然后.我们开始关掉一个broker,在3的控制台里CTRL,C.然后是否终止操作,输入Y.
再发一条消息
![broker3宕机](/images/kafka_broker3_down.jpg)
一切正常.我们看一下集群信息
![broker3宕机集群](/images/kafka_broker3_down_cluster.jpg)
发现Isr中存活的机器少了3.因为3挂了.
然后我们关掉broker2.这时候,会触发新的leader选举.期望值1变成leader,再发一条消息
![broker2宕机](/images/kafka_broker2_down.jpg)
可以看到生产者发消息过程中,产生了异常,因为和2的连接断开了.但是注意,消息并没有丢,因为触发了新的选举.可以看到,消费者还是接到了正常的消息.集群情况如下
![broker2宕机集群](/images/kafka_broker2_down_cluster.jpg)

至此,kafka的broker集群测试完毕,那么剩下的问题来了.消费者启动的时候连接的是zookeeper的地址,如果这台zookeeper挂了呢.
那么我们需要zookeeper集群部署.

## zookeeper集群 ##
这就包括两部分.
1. 是broker本来要能知道这些zookeeper集群的地址,当一个宕机的时候,才会切换到另一个zookeeper
2. 消费者要知道这些zookeeper的地址,理由同上.

因此步骤如下.可以自己试一试,比较简单
1. 复制3份zookeeper.properties文件,命名为zookeeper1.properties,zookeeper2.properties,zookeeper3.properties,修改文件中的dataDir=/tmp/zookeeper和,clientPort=2181,端口分别设置为2181,2182,2183.然后启动三个zookeeper
2. 修改kafka启动配置,server1.properties三个文件中的zookeeper.connect=localhost:2181这个配置,逗号隔开.最终为zookeeper.connect=localhost:2181,localhost:2182,localhost:2183,然后启动
3. 生产者也改下配置中的.metadata.broker.list=localhost:9092,如果使用命令行启动就不用改了.参数指定也可以.
4. 消费者同理,可以改下配置文件中zookeeper.connect=127.0.0.1:2181,也可以命令行启动的时候修改.
5.最终就是各种宕机测试了.
