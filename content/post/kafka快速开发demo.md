---
title: kafka快速开发demo
date: 2015-09-08 00:04:31
tags:
 - 中间件
 - 学习
categories: 
  - 学习笔记
---
在[kafka快速上手](http://leaver.me/2015/09/03/kafka%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B/),主要是使用kafka提供的测试来做了一下简单测试,实际开发中的使用可能才是我们要关系的.启动zk和kafka,新建topic的过程都不变.

1 新建一个maven工程,引入依赖
```
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka_2.11</artifactId>
        <version>0.8.2.1</version>
    </dependency>
```
2 编写配置文件
```
    public interface KafkaProperties {
        public final static String ZK              = "127.0.0.1:2181";
        public final static String GROUP_ID        = "test_group1";
        public final static String TOPIC           = "test";
        public final static String BROKER_LIST     = "127.0.0.1:9092";
        public final static String    SESSION_TIMEOUT = "20000";
        public final static String    SYNC_TIMEOUT    = "20000";
        public final static String    INTERVAL        = "1000";

    }
```

3 编写生产者
```
    public class KafkaProducer extends Thread {

        private Producer<Integer, String> producer;
        private String                    topic;
        private Properties                props = new Properties();
        private final int                 SLEEP = 1000 * 3;

        public KafkaProducer(String topic) {
            props.put("serializer.class", "kafka.serializer.StringEncoder");
            //生产者直接和broker列表连接
            props.put("metadata.broker.list", KafkaProperties.BROKER_LIST);
            producer = new Producer<Integer, String>(new ProducerConfig(props));
            this.topic = topic;
        }

        @Override
        public void run() {
            int offsetNo = 1;
            while (true) {
                String msg = new String("Message_" + offsetNo);
                System.out.println("Send->[" + msg + "]");
                producer.send(new KeyedMessage<Integer, String>(topic, msg));
                offsetNo++;
                try {
                    sleep(SLEEP);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }
    }

```

4 编写消费者
```
    public class KafkaConsumer extends Thread {

        private ConsumerConnector consumer;
        private String            topic;
        private final int         SLEEP = 1000 * 3;

        public KafkaConsumer(String topic) {
            consumer = Consumer.createJavaConsumerConnector(this.consumerConfig());
            this.topic = topic;
        }

        private ConsumerConfig consumerConfig() {
            Properties props = new Properties();
            //消费者使用zk的地址获取连接
            props.put("zookeeper.connect", KafkaProperties.ZK);
            props.put("group.id", KafkaProperties.GROUP_ID);
            props.put("zookeeper.session.timeout.ms", KafkaProperties.SESSION_TIMEOUT);
            props.put("zookeeper.sync.time.ms", KafkaProperties.SYNC_TIMEOUT);
            props.put("auto.commit.interval.ms", KafkaProperties.INTERVAL);
            return new ConsumerConfig(props);
        }

        @Override
        public void run() {
            Map<String, Integer> topicCountMap = new HashMap<String, Integer>();
            topicCountMap.put(topic, new Integer(1));
            Map<String, List<KafkaStream<byte[], byte[]>>> consumerMap = consumer
                .createMessageStreams(topicCountMap);
            KafkaStream<byte[], byte[]> stream = consumerMap.get(topic).get(0);
            ConsumerIterator<byte[], byte[]> it = stream.iterator();
            while (it.hasNext()) {
                System.out.println("Receive->[" + new String(it.next().message()) + "]");
                try {
                    sleep(SLEEP);
                    } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }
    }
```

5 编写启动辅助类
```
    public class KafkaClientApp {
        public static void main(String[] args) {

            KafkaProducer pro = new KafkaProducer(KafkaProperties.TOPIC);

            pro.start();

            KafkaConsumer con = new KafkaConsumer(KafkaProperties.TOPIC);

            con.start();
        }
    }
```

之后启动测试一下就好了.
1. [参考来源](http://www.cnblogs.com/smartloli/p/4543211.html)
