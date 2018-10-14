---
title: kafka中的partition和offset
date: 2015-09-04 00:01:21
tags:
 - 中间件
 - 学习
categories: 学习笔记
---

在[kafka快速上手](http://leaver.me/2015/09/03/kafka%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B/)中,留下的问题是关于partition和offset,这篇文章主要解释这个.
## Log机制 ##
说到分区,就要说kafka对消息的存储.在[官方文档](http://kafka.apache.org/documentation.html#replication)中.
![分区读写日志图](/images/kafka_partition_log.jpg)
首先,kafka是通过log(日志)来记录消息发布的.每当产生一个消息,kafka会记录到本地的log文件中,这个log和我们平时的log有一定的区别.这里可以参考一下[The Log](http://www.cnblogs.com/foreach-break/p/notes_about_distributed_system_and_The_log.html),不多解释.

这个log文件默认的位置在config/server.properties中指定的.默认的位置是log.dirs=/tmp/kafka-logs,linux不用说,windows的话就在你对应磁盘的根目录下.我这里是D盘.

#分区partition#
kafka是为分布式环境设计的,因此如果日志文件,其实也可以理解成消息数据库,放在同一个地方,那么必然会带来可用性的下降,一挂全挂,如果全量拷贝到所有的机器上,那么数据又存在过多的冗余,而且由于每台机器的磁盘大小是有限的,所以即使有再多的机器,可处理的消息还是被磁盘所限制,无法超越当前磁盘大小.因此有了partition的概念.

kafka对消息进行一定的计算,通过hash来进行分区.这样,就把一份log文件分成了多份.如上面的分区读写日志图,分成多份以后,在单台broker上,比如快速上手中,如果新建topic的时候,我们选择了`--replication-factor 1 --partitions 2`,那么在log目录里,我们会看到
test-0目录和test-1目录.就是两个分区了.

你可能会想,这特么没啥区别呀.注意,当有了多个broker之后,这个意义就存在了.这里上一张图,原文在参考链接里有
![kafka分布式分区存储](/images/kafka_partition_storage.jpg)
这是一个topic包含4个Partition，2 Replication(拷贝),也就是说全部的消息被放在了4个分区存储,为了高可用,将4个分区做了2份冗余,然后根据[分配算法](http://blog.csdn.net/lizhitao/article/details/41778193).将总共8份数据,分配到broker集群上.

结果就是每个broker上存储的数据比全量数据要少,但每份数据都有冗余,这样,一旦一台机器宕机,并不影响使用.比如图中的Broker1,宕机了.那么剩下的三台broker依然保留了全量的分区数据.所以还能使用,如果再宕机一台,那么数据不完整了.当然你可以设置更多的冗余,比如设置了冗余是4,那么每台机器就有了0123完整的数据,宕机几台都行.需要在存储占用和高可用之间做衡量.
至于宕机后,zookeeper会选出新的partition leader.来提供服务.这个等下篇文章


#偏移offset#

上一段说了分区,分区就是一个有序的,不可变的消息队列.新来的commit log持续往后面加数据.这些消息被分配了一个下标(或者偏移),就是offset,用来定位这一条消息.

消费者消费到了哪条消息,是保持在消费者这一端的.消息者也可以控制,消费者可以在本地保存最后消息的offset,并间歇性的向zookeeper注册offset.也可以重置offset

#如何通过offset算出分区#

其实partition存储的时候,又分成了多个segment(段),然后通过一个index,索引,来标识第几段.这里先可以去看一下本地log目录的分区文件夹.
在我这里,test-0,这个分区里面,会有一个index文件和一个log文件,
![index和log](/images/kafka_index_log.jpg)

对于某个指定的分区,假设每5个消息,作为一个段大小,当产生了10条消息的情况想,目前有会得到(只是解释)
0.index (表示这里index是对0-4做的索引)
5.index (表示这里index是对5-9做的索引)
10.index (表示这里index是对10-15做的索引,目前还没满)
和
0.log
5.log
10.log
,当消费者需要读取offset=8的时候,首先kafka对index文件列表进行二分查找,可以算出.应该是在5.index对应的log文件中,然后对对应的5.log文件,进行顺序查找,5->6->7->8,直到顺序找到8就好了.

具体的算法参看[美团的文章](http://tech.meituan.com/kafka-fs-design-theory.html)好了

## 更多文档 ##
1. [官方文档](http://kafka.apache.org/documentation.html)
2. [Kafka文件存储机制那些事](http://tech.meituan.com/kafka-fs-design-theory.html)
3. [Kafka集群partition replication自动分配分析](http://blog.csdn.net/lizhitao/article/details/41778193)


