---
author: "li_mingxie"
title: "【kafka笔记】kafka_常用命令(2)"
date: 2022-10-06T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`常用命令`** 相关的内容。<!--more-->  

## 1.基础架构

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_02.png)  
![kafka_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_02.png)

## 2.主题命令行操作

#### bin/kafka-topics.sh

```
//连接的 Kafka Broker 主机名称和端口号。 操作的 topic 名称。
--bootstrap-server <String: server toconnect to> --topic <String: topic>
//创建主题。
--create
//删除主题。
--delete
//修改主题。
--alter
//查看所有主题。
--list
//查看主题详细描述。
--describe
//设置分区数。
--partitions <Integer: # of partitions> 
//设置分区副本。
--replication-factor<Integer: replication factor> 
//更新系统默认的配置。
--config <String: name=value>
```

实际操作

```
//查看当前服务器中的所有 topic
bin/kafka-topics.sh --bootstrap-server kafka:9092 --list
//创建 first topic
bin/kafka-topics.sh --bootstrap-server kafka:9092 --create --partitions 1 --replication-factor 3 --topic first
//查看 first 主题的详情
bin/kafka-topics.sh --bootstrap-server kafka:9092 --describe --topic first
//修改分区数(注意:分区数只能增加，不能减少)
bin/kafka-topics.sh --bootstrap-server kafka:9092 --alter --topic first --partitions 3
//删除 topic
bin/kafka-topics.sh --bootstrap-server kafka:9092 --delete --topic first
```

## 3.生产者命令行操作

#### $ bin/kafka-console-producer.sh

```
//连接的 Kafka Broker 主机名称和端口号。
--bootstrap-server <String: server toconnect to> 
//操作的 topic 名称
--topic <String: topic>

//举例
bin/kafka-console-producer.sh --bootstrap-server kafka:9092 --topic first 
>hello world
>atguigu atguigu
```

## 4.消费者命令行操作

#### bin/kafka-console-consumer.sh

```
//连接的 Kafka Broker 主机名称和端口号。
--bootstrap-server <String: server toconnect to>
//操作的 topic 名称。
--topic <String: topic>
//从头开始消费。
--from-beginning
//指定消费者组名称。
--group <String: consumer group id>

//消费 first 主题中的数据
bin/kafka-console-consumer.sh -- bootstrap-server kafka:9092 --topic first
//把主题中所有的数据都读取出来(包括历史数据)。
bin/kafka-console-consumer.sh -- bootstrap-server kafka:9092 --from-beginning --topic first
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
