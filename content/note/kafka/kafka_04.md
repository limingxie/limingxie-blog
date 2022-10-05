---
author: "li_mingxie"
title: "【kafka笔记】kafka_Broker(4)"
date: 2022-10-08T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`Broker`** 相关的内容。<!--more-->  

## 1 Zookeeper 存储的 Kafka 信息

kafka的所有服务器的信息都是存在zookeeper。  
先看看kafka的信息是如何存到zookeeper。  

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_08.png)  
![kafka_08](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_08.png)

```
[zk: localhost:2181(CONNECTED) 2] ls /kafka/brokers/ids
[0, 1, 2]

[zk: localhost:2181(CONNECTED) 15] get /kafka/controller 
{"version":1,"brokerid":0,"timestamp":"1637292471777"}

[zk: localhost:2181(CONNECTED) 16] get /kafka/brokers/topics/first/partitions/0/state
{"controller_epoch":24,"leader":0,"version":1,"leader_epoch":18," isr":[0,1,2]}
```

## 2.Broker主要参数

#### replica.lag.time.max.ms

ISR中，如果Follower长时间未向Leader发送通信请求或同步数据，  
则该Follower将被踢出ISR。该时间阈值，默认30s。

### auto.leader.rebalance.enable

默认是 true。 自动Leader Partition平衡。

#### leader.imbalance.per.broker.percentage

默认是10%。每个broker允许的不平衡的leader的比率。  
如果每个broker超过了这个值，控制器会触发leader的平衡。

#### leader.imbalance.check.interval.seconds

默认值 300 秒。检查 leader 负载是否平衡的间隔时间。

#### log.segment.bytes log.index.interval.bytes

Kafka中log日志是分成一块块存储的，此配置是指log日志划分成块的大小，默认值 1G。

#### log.index.interval.bytes

默认 4kb，kafka里面每当写入了4kb大小的日志 (.log)，  
然后就往 index 文件里面记录一个索引。

#### log.retention.hours

Kafka 中数据保存的时间，默认7天。  

#### log.retention.minutes

Kafka 中数据保存的时间，分钟级别，默认关闭。  

#### log.retention.ms

Kafka 中数据保存的时间，毫秒级别，默认关闭。

#### log.retention.check.interval.ms

检查数据是否保存超时的间隔，默认是5分钟。

#### log.retention.bytes

默认等于-1，表示无穷大。超过设置的所有日志总大小，删除最早的segment。

#### log.cleanup.policy

默认是 delete，表示所有数据启用删除策略;
如果设置值为 compact，表示所有数据启用压缩策略

#### num.io.threads

默认是8。负责写磁盘的线程数。整个参数值要占总核数的50%。

#### num.replica.fetchers

副本拉取线程数，这个参数占总核数的 50%的 1/3

#### num.network.threads

默认是3。数据传输线程数，这个参数占总核数的 50%的 2/3 。

#### log.flush.interval.messages

强制页缓存刷写到磁盘的条数，默认是long的最大值，  
9223372036854775807。一般不建议修改， 交给系统自己管理。

#### log.flush.interval.ms

每隔多久，刷数据到磁盘，默认是null。一般不建议修改，交给系统自己管理。

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
