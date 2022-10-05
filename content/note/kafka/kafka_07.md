---
author: "li_mingxie"
title: "【kafka笔记】kafka_消费者consumer(7)"
date: 2022-10-11T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`消费者`** 相关的内容。<!--more-->  

## 1.Kafka 消费方式

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_16.png)  
![kafka_16](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_16.png)

## 2.Kafka 消费者工作流程

#### 2.1 消费者总体工作流程

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_17.png)  
![kafka_17](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_17.png)

#### 2.2 消费者组Consumer Group

**`Consumer Group(CG):消费者组`**，由多个consumer组成。  
形成一个消费者组的条件，是所有消费者的groupid相同。  

* 消费者组内每个消费者负责消费不同分区的数据，一个分区只能由一个组内消费者消费。
* 消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_18.png)  
![kafka_18](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_18.png)

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_19.png)  
![kafka_19](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_19.png)

## 3.消费者组初始化流程

#### coordinator

辅助实现消费者组的初始化和分区的分配。  
coordinator节点选择 = groupid的hashcode值 % 50( __consumer_offsets的分区数量)  
例如: groupid的hashcode值 = 1，1% 50 = 1，那么__consumer_offsets 主题的1号分区，在哪个broker上,  
就选择这个节点的coordinator作为这个消费者组的老大。  
消费者组下的所有的消费者提交offset的时候就往这个分区去提交offset。  

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_20.png)  
![kafka_20](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_20.png)

#### 消费者组详细消费流程

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_21.png)  
![kafka_21](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_21.png)

## 4.消费者重要参数

**bootstrap.servers**  
向 Kafka 集群建立初始连接用到的 host/port 列表。  

**key.deserializer 和 value.deserializer**  
指定接收消息的key和value的反序列化类型。一定要写全类名。  

**group.id**  
标记消费者所属的消费者组。  

**enable.auto.commit**  
默认值为true，消费者会自动周期性地向服务器提交偏移量。  

**auto.commit.interval.ms**  
如果设置了enable.auto.commit的值为true，则该值定义了消费者偏移量向Kafka提交的频率，默认5s。  

**auto.offset.reset**  
当 Kafka 中没有初始偏移量或当前偏移量在服务器中不存在 (如，数据被删除了)，该如何处理?  
earliest:自动重置偏 移量到最早的偏移量。  
latest:默认，自动重置偏移量为最 新的偏移量。  
none:如果消费组原来的(previous)偏移量 不存在，则向消费者抛异常。  
anything:向消费者抛异常。  

**offsets.topic.num.partitions**  
__consumer_offsets 的分区数，默认是 50 个分区。  

**heartbeat.interval.ms**  
Kafka消费者和coordinator 之间的心跳时间，默认3s。  
该条目的值必须小于session.timeout.ms，也不应该高于session.timeout.ms的1/3。  

**session.timeout.ms**  
Kafka消费者和coordinator之间连接超时时间，默认45s。  
超过该值，该消费者被移除，消费者组执行再平衡。  

**max.poll.interval.ms**  
消费者处理消息的最大时长，默认是 5分钟。超过该值，该消费者被移除，消费者组执行再平衡。  

**fetch.min.bytes**  
默认1个字节。消费者获取服务器端一批消息最小的字节数。  

**fetch.max.wait.ms**  
默认500ms。如果没有从服务器端获取到一批数据的最小字节数。该时间到，仍然会返回数据。  

**fetch.max.bytes**  
默认Default: 52428800(50 m)。  
消费者获取服务器端一批 消息最大的字节数。  
如果服务器端一批次的数据大于该值 (50m)仍然可以拉取回来这批数据，因此，这不是一个绝对最大值。  
一批次的大小受 message.max.bytes (broker config)or max.message.bytes (topic config)影响。  

**max.poll.records**  
一次poll拉取数据返回消息的最大条数，默认是500条。  

## 5.消费者分区分配策略

一个consumer group中有多个consumer组成，一个 topic有多个partition组成，  
现在的问题是，到底由哪个consumer来消费哪个 partition的数据。

#### Kafka有四种主流的分区分配策略: Range、RoundRobin、Sticky、CooperativeSticky  

可以通过配置参数partition.assignment.strategy，修改分区的分配策略。  
默认策略是Range+ CooperativeSticky。Kafka可以同时使用多个分区分配策略。

**heartbeat.interval.ms**  
Kafka 消费者和 coordinator 之间的心跳时间，默认 3s。
该条目的值必须小于 session.timeout.ms，也不应该高于 session.timeout.ms 的 1/3。  

**session.timeout.ms**  
Kafka 消费者和 coordinator 之间连接超时时间，默认 45s。  
超过该值，该消费者被移除，消费者组执行再平衡。  

**max.poll.interval.ms**  
消费者处理消息的最大时长，默认是 5 分钟。超过该值，该消费者被移除，消费者组执行再平衡。  

**partition.assignment.strategy**  
消费者分区分配策略，默认策略是 Range +CooperativeSticky。  
Kafka 可以同时使用多个分区分配策略。 可以选择的策略包括:Range、RoundRobin、Sticky、 CooperativeSticky

#### 5.1 Range 分区策略

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_22.png)  
![kafka_22](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_22.png)

#### 5.2 RoundRobin 分区策略

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_23.png)  
![kafka_23](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_23.png)

#### 5.3 Sticky 分区策略

**粘性分区定义:**可以理解为分配的结果带有“粘性的”。  
即在执行一次新的分配之前，考虑上一次分配的结果，尽量少的调整分配的变动，可以节省大量的开销。  
粘性分区是 Kafka 从 0.11.x 版本开始引入这种分配策略，  
首先会尽量均衡的放置分区到消费者上面，在出现同一消费者组内消费者出现问题的时候，会尽量保持原有分配的分区不变化。

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
