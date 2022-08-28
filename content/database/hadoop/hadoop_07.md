---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_yarn简介(7)"
date: 2022-08-20T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

Yarn 是一个资源调度平台，负责为运算程序提供服务器运算资源，  
相当于一个分布式 的操作系统平台，而MapReduce等运算程序则相当于运行于操作系统之上的应用程序。
<!--more-->

## 1.Yarn基础架构

YARN 主要由 ResourceManager、NodeManager、ApplicationMaster 和 Container 等组件构成。

## 2.YARN基础架构

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_01.png)  
![yarn_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_01.png)

----------------------------------------------

## 3.YARN工作机制

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_02.png)  
![yarn_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_02.png)

1. MR 程序提交到客户端所在的节点。
2. YarnRunner 向 ResourceManager 申请一个 Application。
3. RM 将该应用程序的资源路径返回给 YarnRunner。
4. 该程序将运行所需资源提交到 HDFS 上。
5. 程序资源提交完毕后，申请运行 mrAppMaster。
6. RM 将用户的请求初始化成一个 Task。
7. 其中一个 NodeManager 领取到 Task 任务。
8. 该 NodeManager 创建容器 Container，并产生 MRAppmaster。
9. Container 从 HDFS 上拷贝资源到本地。
10. MRAppmaster 向 RM 申请运行 MapTask 资源。
11. RM 将运行 MapTask 任务分配给另外两个 NodeManager，另两个 NodeManager 分别领取任务并创建容器。
12. MR 向两个接收到任务的 NodeManager 发送程序启动脚本，这两个 NodeManager分别启动 MapTask，MapTask 对数据分区排序。
13. MrAppMaster 等待所有 MapTask 运行完毕后，向 RM 申请容器，运行 ReduceTask。 (14)ReduceTask 向 MapTask 获取相应分区的数据。
15. 程序运行完毕后，MR 会向 RM 申请注销自己。

----------------------------------------------

## 4.HDFS、YARN、MapReduce三者关系

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_03.png)  
![yarn_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_03.png)

----------------------------------------------

## 5.YARN作业提交过程

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_04.png)  
![yarn_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_04.png)

----------------------------------------------

## 6.HDFS & MapReduce 作业提交过程

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_05.png)  
![yarn_05](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_05.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
