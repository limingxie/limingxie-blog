---
author: "li_mingxie"
title: "【zookeeper笔记】zookeeper_启动和配置(1)"
date: 2022-08-11T23:28:49+08:00
tags: [
    "zookeeper",
]
categories: [
    "zookeeper",
]
---

简单的整理了zookeeper启动和相关配置。  <!--more-->

## 1.单节点启动

```
#run zookeeper
docker run --name some-zookeeper --restart always -d zookeeper
```

```
#status
$ docker exec -i zookeeper zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone
```

## 2.集群模式

创建stack.yml

```yml
version: '3.1'

services:
  zoo1:
    image: zookeeper
    restart: always
    hostname: zoo1
    ports:
      - 2181:2181
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo2:
    image: zookeeper
    restart: always
    hostname: zoo2
    ports:
      - 2182:2181
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo3:
    image: zookeeper
    restart: always
    hostname: zoo3
    ports:
      - 2183:2181
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181
```

```bash
docker-compose -f stack.yml up
```

查看状态

```bash
root@zoo1:/apache-zookeeper-3.8.0-bin/bin# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
#Mode: leader
```

## 3.conf/zoo.cfg

zookeeper的常用配置解释

```bash
# 数据路径
dataDir=/data
# 日志存储路径
dataLogDir=/datalog
# 心跳毫秒
tickTime=2000
# initLimit*tickTime 服务启动时 判断Leader和flower的链接是否正常
initLimit=5
# syncLimit*tickTime 服务启动后 判断Leader和flower的链接是否正常
syncLimit=2
# 客户端连接server的port，即对外服务port，一般设置为2181
clientPort=2181 
# 这个參数和上面的參数搭配使用，这个參数指定了须要保留的文件数目。默认是保留3个。
autopurge.snapRetainCount=3
# 3.4.0及之后版本号，ZK提供了自己主动清理事务日志和快照文件的功能，这个參数指定了清理频率。
# 单位是小时。须要配置一个1或更大的整数，默认是0。表不开启自己主动清理功能
autopurge.purgeInterval=0
# 单个client与单台server之间的连接数的限制，是ip级别的，默认是60。
maxClientCnxns=60
standaloneEnabled=true
admin.enableServer=true
#ZK集群节点配置，2181 对 client 端提供服务，端口号2888用于集群节点之间数据通信，端口号3888用于集群中Leader选举
server.1=localhost:2888:3888;2181
```

## 4.zookeeper 的三个端口作用

* 2181 : 对 client 端提供服务
* 2888 : 集群内机器通信使用
* 3888 : 选举 leader 使用

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
