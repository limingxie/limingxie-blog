---
author: "li_mingxie"
title: "【kafka笔记】kafka_生产者(3)"
date: 2022-10-07T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`生产者消息发送`** 相关的内容。<!--more-->  

## 1.基础架构

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_03.png)  
![kafka_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_03.png)

## 2.生产者主要参数列表

#### bootstrap.servers

生产者连接集群所需的 broker 地址清单。  
例如 kafka:9092,kafka:9092,kafka:9092，  
可以 设置 1 个或者多个，中间用逗号隔开。  
注意这里并非需要所有的 broker 地址，因为生产者从给定的 broker 里查找到其他 broker 信息。

#### key.serializer 和 value.serializer

指定发送消息的 key 和 value 的序列化类型。一定要写 全类名。

#### buffer.memory

ecordAccumulator 缓冲区总大小，默认 32m。

#### batch.size

缓冲区一批数据最大值，默认16k。适当增加该值，可以提高吞吐量，  
但是如果该值设置太大，会导致数据传输延迟增加。

#### linger.ms

如果数据迟迟未达到 batch.size，sender 等待 linger.time 之后就会发送数据。  
单位 ms，默认值是 0ms，表示没 有延迟。生产环境建议该值大小为 5-100ms 之间。

#### acks

* 0:生产者发送过来的数据，不需要等数据落盘应答。
* 1:生产者发送过来的数据，Leader 收到数据后应答。 -1
* (all):生产者发送过来的数据，Leader+和 isr 队列 里面的所有节点收齐数据后应答。
    默认值是-1，-1 和 all 是等价的。

#### max.in.flight.requests.per.connection

允许最多没有返回 ack 的次数，默认为 5，开启幂等性 要保证该值是 1-5 的数字。

#### retries

当消息发送出现错误的时候，系统会重发消息。  
retries 表示重试次数。默认是 int 最大值，2147483647。  
如果设置了重试，还想保证消息的有序性，需要设置 MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION=1 否则在重试此失败消息的时候，其他的消息可能发送 成功了。

#### retry.backoff.ms

两次重试之间的时间间隔，默认是 100ms。

#### enable.idempotence

是否开启幂等性，默认 true，开启幂等性。

#### compression.type

生产者发送的所有数据的压缩方式。默认是 none，也 就是不压缩。
支持压缩类型:none、gzip、snappy、lz4 和 zstd。

## 3.消息发送

* 异步发送 API
* 同步发送 API

可通过acks值去判断

## 4.生产者分区

1. 便于合理使用存储资源，每个Partition在一个Broker上存储，  
    可以把海量的数据按照分区切割成一块一块数据存储在多台Broker上。  
    合理控制分区的任务，可以实现负载均衡的效果。
2. 提高并行度，生产者可以以分区为单位发送数据;消费者可以以分区为单位进行消费数据.

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_04.png)  
![kafka_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_04.png)

## 5.生产者吞吐量

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_05.png)  
![kafka_05](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_05.png)

## 6.生产者数据的可靠性

* acks=0，生产者发送过来数据就不管了，可靠性差，效率高;
* acks=1，生产者发送过来数据Leader应答，可靠性中等，效率中等;
* acks=-1，生产者发送过来数据Leader和ISR队列里面所有Follwer应答，可靠性高，效率低;

> 在生产环境中，acks=0很少使用;  
> acks=1，一般用于传输普通日志，允许丢个别数据;  
> acks=-1，一般用于传输和钱相关的数据， 对可靠性要求比较高的场景。  

## 7.生产者数据去重

* 至少一次(AtLeastOnce)=ACK级别设置为-1 + 分区副本大于等于2 + ISR里应答的最小副本数量大于等于2
* 最多一次(AtMostOnce)=ACK级别设置为0

> At Least Once 可以保证数据不丢失，但是不能保证数据不重复;  
> At Most Once 可以保证数据不重复，但是不能保证数据不丢失。  

* 精确一次(Exactly Once):对于一些非常重要的信息，比如和钱相关的数据，要求数据既不能重复也不丢失。
    Kafka 0.11版本以后，引入了一项重大特性:幂等性和事务。

## 8.生产者幂等性

幂等性就是指Producer不论向Broker发送多少次重复数据，Broker端都只会持久化一条，保证了不重复。

**精确一次(Exactly Once)** = 幂等性 + 至少一次( ack=-1 + 分区副本数>=2 + ISR最小副本数量>=2) 。

**重复数据的判断标准**:具有<PID, Partition, SeqNumber>相同主键的消息提交时，Broker只会持久化一条。  
其中PID是Kafka每次重启都会分配一个新的;Partition 表示分区号;Sequence Number是单调自增的。

#### 所以幂等性只能保证的是在单分区单会话内不重复

开启参数 `enable.idempotence` 默认为 true，false 关闭。

## 9.生产者事务

> 开启事务，必须开启幂等性。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_06.png)  
![kafka_06](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_06.png)

**事务一般步骤**  

1. 初始化事务
2. 开启事务
3. 在事务内提交已经消费的偏移量(主要用于消费者)
4. 提交事务
5. 放弃事务(类似于回滚事务的操作)

## 10.数据的顺序

1. kafka在1.x版本之前保证数据单分区有序，  
    条件如下: `max.in.flight.requests.per.connection`=1(不需要考虑是否开启幂等性)。  
2. kafka在1.x及以后版本保证数据单分区有序，  
    条件如下:  
    (1)未开启幂等性  
    `max.in.flight.requests.per.connection`需要设置为1。  
    (2)开启幂等性  
    `max.in.flight.requests.per.connection`需要设置小于等于5。  
    原因说明:因为在kafka1.x以后，启用幂等后，kafka服务端会缓存producer发来的最近5个request的元数据，  
    所以无论如何，都可以保证最近5个request的数据都是有序的。  

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_07.png)  
![kafka_07](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_07.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
