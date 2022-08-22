---
author: "li_mingxie"
title: "【zookeeper笔记】zookeeper_监听和写入操作(3)"
date: 2022-08-13T23:28:49+08:00
tags: [
    "zookeeper",
]
categories: [
    "zookeeper",
]
---

这一篇简单的整理了zookeeper的监听和写入操作。  <!--more-->

## 1.监听原理

[图片备用地址](https://limingxie.github.io/images/system/zk/zk_01.png)  
![zk_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/zk/zk_01.png)

1. 启动main()线程。
2. 在main线程中创建Zookeeper客户端，同时创建2个线程。  
一个负责网络连接通信(connect)。  
一个是负责监听(listener)。  
3. 通过connect线程将注册的监听时间发送给zookeeper。
4. 将注册的监听事件添加到，zookeeper的注册监听器类表中。
5. zookeeper监听到有数据或路径变化，会将这个消息发送给listener线程。
6. listener线程内部调用了process()方法。

#### 常见的监听类型

* 监听节点数据变化  
get -w path
* 监听子节点增减的变化  
ls -w path

## 2.写入流程

1. client像zookeeper的Server1上发一个写数据请求。
2. 如果Server1不是Leader，会把接受到的请求进一步转发给Leader。  
Leader会把写请求广播给每个Server。各Server写成功后悔通知Leader.
3. 当Leader收到大部分Server数据写入成功，Leader会告诉Server1数据写入成功。
(大部分是只一般节点以上，比如有5个节点，那成功3个节点就认为大部分都成功了。)
4. Server1会通知Client数据写入成功，写操作结束。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
