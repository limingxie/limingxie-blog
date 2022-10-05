---
author: "li_mingxie"
title: "【kafka笔记】kafka_副本Repllicas(5)"
date: 2022-10-09T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`副本Repllicas`** 相关的内容。<!--more-->  

## 1.基本信息

1. Kafka 副本作用:提高数据可靠性。
2. Kafka 默认副本 1 个，生产环境一般配置为 2 个，保证数据可靠性;太多副本会 增加磁盘存储空间，增加网络上数据传输，降低效率。
3. Kafka 中副本分为:Leader 和 Follower。Kafka 生产者只会把数据发往 Leader， 然后 Follower 找 Leader 进行同步数据。
4. Kafka 分区中的所有副本统称为 AR(Assigned Repllicas)。  
    AR = ISR + OSR  
    ISR，表示和 Leader 保持同步的 Follower 集合。  
    如果 Follower 长时间未向 Leader 发送通信请求或同步数据，  
    则该 Follower 将被踢出 ISR。该时间阈值由 replica.lag.time.max.ms 参数设定，默认 30s。  
    Leader 发生故障之后，就会从 ISR 中选举新的 Leader。
    OSR，表示 Follower 与 Leader 副本同步时，延迟过多的副本。

## 2 Leader 选举流程

Kafka 集群中有一个broker的Controller会被选举为Controller Leader，负责管理集群 broker的上下线，  
所有 topic 的分区副本分配和 Leader 选举等工作。
Controller 的信息同步工作是依赖于Zookeeper。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_09.png)  
![kafka_09](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_09.png)

## 3.Leader和Follower故障处理细节

#### 3.1 Follower故障处理细节

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_10.png)  
![kafka_10](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_10.png)

#### 3.2 Leader故障处理细节

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_11.png)  
![kafka_11](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_11.png)

#### 3.3 Leader Partition自动平衡

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_12.png)  
![kafka_12](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_12.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
