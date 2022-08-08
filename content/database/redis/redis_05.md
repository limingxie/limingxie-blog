---
author: "li_mingxie"
title: "【redis笔记】redis_主从复制(5)"
date: 2022-08-06T23:28:49+08:00
tags: [
    "redis",
]
categories: [
    "redis"
]
---

这一篇整理了redis主从复制相关的内容。<!--more-->  

## 1.配置项

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

#启动时直接配置为从机，设置主机的地址
slaveof localhost 6379
```

## 2.常用命令

```redis
#打印主从复制的相关信息
info replication

#成为某个实例的从服务器
slaveof  <ip>  <port>  
```

## 3.复制原理

* 每次从机联通后，都会给主机发送sync指令
* 主机立刻进行存盘操作，发送RDB文件，给从机
* 从机收到RDB文件后，进行全盘加载
* 之后每次主机的写操作，都会立刻发送给从机，从机执行相同的命令

## 4.主机->从机->从机

```redis
#可以在从机下面在链接从机，减少master的压力
slaveof  <ip>  <port>
```

## 5.从机to主机

```redis
#将从机变为主机
slaveof  no one 
```

## 6.哨兵模式(sentinel)

1. 新建sentinel.conf文件
2. 在配置文件中填写内容:  
    sentinel  monitor  mymaster  127.0.0.1  6379  1
3. 其中mymaster为监控对象起的服务器名称， 1 为 至少有多少个哨兵同意迁移的数量。
4. 启动哨兵:  
    执行redis-sentinel  /myredis/sentinel.conf

* 优先级在redis.conf中slave-priority 100  
* 偏移量是指获得原主数据最多的(有最新数据)  
* 每个redis实例启动后都会随机生成一个40位的runid  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
