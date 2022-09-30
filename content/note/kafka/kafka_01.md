---
author: "li_mingxie"
title: "【kafka笔记】kafka_定义和启动(1)"
date: 2022-10-05T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`定义和启动`** 相关的内容。<!--more-->  

## 1.定义

#### Kafka传统定义

Kafka是一个分布式的基于发布/订阅模式的消息队列(Message Queue)，主要应用于大数据实时处理领域。  
发布/订阅:消息的发布者不会将消息直接发送给特定的订阅者，而是将发布的消息 分为不同的类别，订阅者只接收感兴趣的消息。  

#### Kafka最新定义

Kafka是一个开源的分布式事件流平台(Event Streaming Platform)，  
被数千家公司用于高性能数据管道、流分析、数据集成和关键任务应用。

## 2.基础架构

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_01.png)  
![kafka_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_01.png)

#### 2.1 Producer

消息生产者，就是向 Kafka broker 发消息的客户端。

#### 2.2 Consumer

消息消费者，向 Kafka broker 取消息的客户端。

#### 2.3 Consumer Group(CG)

消费者组，由多个consumer组成。消费者组内每个消
费者负责消费不同分区的数据，一个分区只能由一个组内消费者消费;消费者组之间互不 影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。

#### 2.4 Broker

一台 Kafka 服务器就是一个 broker。一个集群由多个 broker 组成。一个 broker 可以容纳多个 topic。

#### 2.5 Topic

可以理解为一个队列，生产者和消费者面向的都是一个 topic。

#### 2.6 Partition

为了实现扩展性，一个非常大的 topic 可以分布到多个 broker(即服 务器)上，一个 topic 可以分为多个 partition，每个 partition 是一个有序的队列。

#### 2.7 Replica

副本。一个 topic 的每个分区都有若干个副本，一个 Leader 和若干个 Follower。

#### 2.8 Leader

每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数 据的对象都是 Leader。

#### 2.9 Follower

每个分区多个副本中的“从”，实时从 Leader 中同步数据，保持和 Leader 数据的同步。Leader 发生故障时，某个 Follower 会成为新的 Leader。

## 3.容器启动kafka

我使用的是`bitnami/kafka`镜像  
详细的可以参考 ==> <https://hub.docker.com/r/bitnami/kafka>  

```bash
curl -sSL https://raw.githubusercontent.com/bitnami/containers/main/bitnami/kafka/docker-compose.yml > docker-compose.yml
docker-compose up -d
```

容器中bin目录的sh脚本在一下路径下

```bash
cd /opt/bitnami/kafka/bin

//producer 给 test topic 发消息
kafka-console-producer.sh --broker-list kafka:9092 --topic test

//consumer 订阅 test 消息
kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic test
```

## 4.config配置

#### 4.1 config/server.properties

```
#broker 的全局唯一编号，不能重复，只能是数字。 broker.id=0
... ...
#kafka 运行日志(数据)存放的路径，路径不需要提前创建，kafka 自动帮你创建，可以 配置多个磁盘路径，路径与路径之间可以用"，"分隔  
log.dirs=/opt/module/kafka/datas
... ...
#配置连接 Zookeeper 集群地址(在 zk 根目录下创建/kafka，方便管理)
zookeeper.connect=kafka:2181,kafka:2181,kafka:2181/kafka
```

#### 4.2 环境变量

```
#KAFKA_HOME
export KAFKA_HOME=/opt/module/kafka 
export PATH=$PATH:$KAFKA_HOME/bin
```

#### 4.3 启动集群

先启动 Zookeeper 集群，然后启动 Kafka。

```
zk.sh start
//启动服务
bin/kafka-server-start.sh -daemon config/server.properties
//停止服务
bin/kafka-server-stop.sh
```

> 停止Kafka集群时，一定要等Kafka所有节点进程全部停止后再停止Zookeeper 集群。  
> 因为Zookeeper 集群当中记录着Kafka集群相关信息，Zookeeper集群一旦先停止，
> Kafka 集群就没有办法再获取停止进程的信息，只能手动杀死 Kafka 进程了

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
