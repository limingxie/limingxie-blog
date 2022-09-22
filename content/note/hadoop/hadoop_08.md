---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_yarn调度器和调度算法(8)"
date: 2022-08-21T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这一篇整理了Yarn调度器和调度算法相关的内容。
<!--more-->

## 1.Yarn调度器和调度算法

Hadoop 作业调度器主要有三种:FIFO、容量(Capacity Scheduler)和公平(Fair Scheduler)。  
Apache Hadoop3.1.3 默认的资源调度器是 Capacity Scheduler。  
CDH 框架默认调度器是 Fair Scheduler。  

## 2.先进先出调度器(FIFO)

优点:简单易懂;  
缺点:不支持多队列，生产环境很少使用  

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_06.png)  
![yarn_06](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_06.png)

----------------------------------------------

## 3.容量调度器(Capacity Scheduler)

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_07.png)  
![yarn_07](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_07.png)

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_08.png)  
![yarn_08](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_08.png)

----------------------------------------------

## 4.公平调度器(Fair Scheduler)

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_09.png)  
![yarn_09](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_09.png)

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_10.png)  
![yarn_10](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_10.png)

----------------------------------------------

#### 4.1 公平调度器资源分配算法

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_12.png)  
![yarn_12](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_12.png)

#### 4.2 公平调度器队列资源分配方式

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_13.png)  
![yarn_13](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_13.png)

----------------------------------------------

## 5.Yarn 生产环境核心参数

[图片备用地址](https://limingxie.github.io/images/database/hadoop/yarn_14.png)  
![yarn_14](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/yarn_14.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
