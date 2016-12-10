title: graylog日记管理平台使用的那些坑
date: 2016-12-10 18:48:17
tags:
  - java
  - 工作
categories:
  - 学习笔记
---

## 前言

最近使用 graylog在部署日志平台的时候,踩到很多"坑",记录一下

## 日志采集(nxlog)

### 1.客户端不要做太多的正则计算

graylog 最早推荐的 nxlog 采集客户端,现在貌似有了 beats 的采集方式,不过我没了解,nxlog 采集的话,需要配置Snippets,就是定义输入,输出,处理器的地方,这个地方, Input 模块是在客户端计算的.所以,一定不要进行太多的正则计算.否则会严重影响客户端的 cpu 资源.降低应用程序的性能.

### 2.开多行一定要慎重

graylog 可以通过配置

````
<Extension multiline>
    Module    xm_multiline
    HeaderLine    /^\d{0,2}\/\d{0,2}\/\d{0,4}/
    EndLine    /^\d{0,2}\/\d{0,2}\/\d{0,4}/
</Extension>

<Input pcc-esolutions-log>

    Module            im_file
    File            "*.log"
    SavePos            TRUE
    InputType    multiline
</Input>
````
来实现对于类似错误栈这样的信息,将多行采集成一行,但是一定要注意.如果这个正则写错了,或者其他原因,导致,未能正确匹配.会导致 nxlog 客户端占用内存暴涨.原因是为了实现多行采集,会再客户端内存中保存日志内容,直到匹配到行尾.如果未能正确匹配.会一直保存.导致内存泄露.

这时候一般伴随着nxlog 的客户端日志中开始打印:
`2016-12-05 18:36:47 ERROR oversized string, limit is 1048576 bytes`
这样的信息.表示单条日志超过了1m

最终有一定几率影响客户端应用,被 oom 所杀.不要问我怎么知道的...

### 3 日志就是太大怎么办.

貌似没办法..只能在 Input 配置中.
````
Exec if $raw_event $raw_event = substr($raw_event, 0, 1040000);

````
执行类似的来限制,没有尝试过,参考这里:[日志大小超长配置](https://github.com/cityindex/LogSearchShipper/blob/master/src/LogsearchShipper.Core/NxLog/NxLogProcessManager.cs)


## 服务端处理(graylog)


### 1.服务端性能不好的情况下也不要做大量正则

 日志处理这部分主要是说 graylog 自身的处理,graylog 是 cpu 密集型的,在收到了 nxlog 经过少量计算的日志后, graylog 其实还提供了 extrator 的功能来解析字段,当时我因为部署了很多应用的日志采集,为了生成一个统一的索引字段,我在extrator写了一个正则,对于所有的消息,根据这个正则找到一个字段,来作为 key(保存成 no), 可能一个流水号,这样我就可以根据 no:xxx 来查询所有相关的日志了.

结果这个正则写了以后, graylog 处理性能急剧下降.开始大量积压消息.无法发送给后端的 es 来做处理.在 graylog 的管理页面,能明显看到 in 是几千, out 是几百..很快 node 节点就废了.

参考:[Very slow process message](https://github.com/Graylog2/graylog2-server/issues/1334)


如果是确定不是 graylog 的问题, output 还是慢,可以尝试[修改输出的并发量来解决](https://github.com/Graylog2/graylog2-server/issues/2313),改改 graylog 配置中的output_batch_size值.


### 2.journal如果太多,可能导致graylog 状态 dead

 由于我前面的问题,导致 journal 中保存了太多的日志,这样会导致两个问题,1,启动的时候会尝试吧这些日志全部加载 graylog 服务端的内存中.这时候,如果应用内存不够,直接会启动不了报java 的 oom,
 
````
 2016-12-04T12:25:36.543+02:00 ERROR [ServiceManager] Service JournalReader [FAILED] has failed in the RUNNING state.
java.lang.OutOfMemoryError: Java heap space
	at java.nio.HeapByteBuffer.<init>(HeapByteBuffer.java:57)
	at java.nio.ByteBuffer.allocate(ByteBuffer.java:331)
	at kafka.log.FileMessageSet$$anon$1.makeNext(FileMessageSet.scala:188)

````
 或者会报一个数组越界的错误.能解决的办法就是删..
 还有就是临时加大 graylog的 jvm 内存设置.


### 3.保持时钟同步

 新部署的一个 graylog 节点,启动一直报错
 `2016-12-10T20:29:27.304+08:00 WARN  [NodePingThread] Did not find meta info of this node. Re-registering.`
 在 graylog 的页面上也看不到,后来看 [issue](https://github.com/Graylog2/graylog2-server/issues/2404) 发现是时钟同步的问题..同步一下即可.



## es 存储 

### 1.unassigned shards解决

如果 es 集群因为某个原因挂掉,可能会产生大量的无法自行处理.参考[ELASTICSEARCH几个问题的解决](http://www.wklken.me/posts/2015/05/23/elasticsearch-issues.html)解决即可.