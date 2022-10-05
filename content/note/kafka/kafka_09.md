---
author: "li_mingxie"
title: "【kafka笔记】kafka_EFAK(9)"
date: 2022-10-13T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`EFAK`** 相关的内容。<!--more-->  

## 1.安装

官网: <https://www.kafka-eagle.org/>

使用eagle需要修改内存空间大小  
/opt/module/kafka/bin/kafka-server-start.sh

```bash
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
export KAFKA_HEAP_OPTS="-server -Xms2G -Xmx2G - XX:PermSize=128m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=8 -XX:ConcGCThreads=5 - XX:InitiatingHeapOccupancyPercent=70"
    export JMX_PORT="9999"
#export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G" 
fi
```

## 2.配置文件

配置文件在conf文件架下面  
/opt/module/efak/conf/system-config.properties  

需要有2个地方要修改。

```bash
... ...
efak.zk.cluster.alias=cluster1 
cluster1.zk.list=kafka01:2181,kafka02:2181/kafka
... ...
# 配置 mysql 连接
efak.driver=com.mysql.jdbc.Driver efak.url=jdbc:mysql://kafka:3306/ke?useUnicode=true&characterEncoding=UT F-8&zeroDateTimeBehavior=convertToNull
efak.username=root
efak.password=1234
... ...
```

## 3.EFAK profile

```bash
vi /etc/profile
export KE_HOME=/data/soft/new/efak
export PATH=$PATH:$KE_HOME/binTH=$PATH:$JAVA_HOME/bin
```

## 4.Start the EFAK server (Standalone)

```bash
cd ${KE_HOME}/bin
chmod +x ke.sh
ke.sh start

ke.sh restart
ke.sh stop
```

## 5.界面

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_30.png)  
![kafka_30](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_30.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
