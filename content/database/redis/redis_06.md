---
author: "li_mingxie"
title: "【redis笔记】redis_集群(6)"
date: 2022-08-07T23:28:49+08:00
tags: [
    "redis",
]
categories: [
    "redis"
]
---

这一篇整理了redis集群相关的内容。<!--more-->  

Redis集群实现了对Redis的水平扩容，即启动N个redis节点，  
将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

## 1、安装ruby环境

```
yum install ruby
yum install rubygems

#安装redis-3.2.0.gem
gem install --local redis-3.2.0.gem
```

#### 配置项

* redis.conf文件include
* 开启daemonize yes
* Pid文件名字pidfile
* 指定端口port
* Log文件名字
* Dump.rdb名字dbfilename
* Appendonly 关掉或者换名字

```
include /opt/module/redis/conf/redis.conf
pidfile var/run/redis_6379.pid
port 6379
dbfilename dump6379.rdb

cluster-enabled yes
cluster-config-file  nodes-6379.conf
cluster-node-timeout 15000
```

#### 创建集群环境

```
cd  /opt/redis-3.2.5/src
./redis-trib.rb create --replicas 1 192.168.31.211:6379 192.168.31.211:6380 192.168.31.211:6381 192.168.31.211:6389 192.168.31.211:6390 192.168.31.211:6391
```

> 此处不要用127.0.0.1， 请用真实IP地址

```
#查看集群信息
cluster nodes
```

## 2.redis cluster 节点

* 一个集群至少要有三个主节点。
* 选项 --replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。
* 分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。

> 主节点宕机，从机自动上升为主节点，这次再次启动原来主节点，它会成为从机。

```
#redis.conf中的参数决定当一个节点全部(主从)宕机时，  
#是能继续用(部分节点有效)还是不不能用(所有集群都不能用)。
cluster-require-full-coverage
```

## 3.slots

一个 Redis 集群包含 16384 个插槽（hash slot）  

```
比如分3个主节点，其中：  
节点 A 负责处理 0 号至 5500 号插槽。  
节点 B 负责处理 5501 号至 11000 号插槽。  
节点 C 负责处理 11001 号至 16383 号插槽。  
```

redis-cli客户端提供了 –c 参数实现自动重定向。  
不在一个slot下的键值，是不能使用mget,mset等多键操作。  

```
#集群方式连接
redis-cli -c -p ...

#计算键 key 应该被放置在哪个槽上。
CLUSTER KEYSLOT <key> 

#返回槽 slot 目前包含的键值对数量。  
CLUSTER COUNTKEYSINSLOT <slot> 

#返回 count 个 slot 槽中的键。
CLUSTER GETKEYSINSLOT <slot> <count> 
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
