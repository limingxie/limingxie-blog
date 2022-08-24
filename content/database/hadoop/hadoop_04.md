---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_MapReduce简介(4)"
date: 2022-08-17T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这篇文章整理了MapReduce相关的内容。<!--more-->

## 1.MapReduce定义

map_reduce定义
MapReduce 是一个分布式运算程序的编程框架，是用户开发“基于Hadoop的数据分析应用”的核心框架。  
MapReduce 核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个Hadoop集群上。

## 2.MapReduce基本流程

[图片备用地址](https://limingxie.github.io/images/database/hadoop/map_reduce_01.png)  
![map_reduce_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/map_reduce_01.png)

## 3.MapReduce 优缺点

#### 3.1 优点

1. MapReduce 易于编程  
2. 良好的扩展性  
3. 高容错性  
4. 适合 PB 级以上海量数据的离线处理  

#### 3.2 缺点

1. 不擅长实时计算  
  MapReduce 无法像 MySQL 一样，在毫秒或者秒级内返回结果
2. 不擅长流式计算  
  流式计算的输入数据是动态的，而 MapReduce 的输入数据集是静态的，不能动态变化。 这是因为 MapReduce 自身的设计特点决定了数据源必须是静态的。
3. 不擅长 DAG(有向无环图)计算  
  多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下， MapReduce 并不是不能做，而是使用后，每个 MapReduce 作业的输出结果都会写入到磁盘， 会造成大量的磁盘 IO，导致性能非常的低下。

## 4.MapReduce 进程

一个完整的 MapReduce 程序在分布式运行时有三类实例进程:
  
1. MrAppMaster:负责整个程序的过程调度及状态协调。  
2. MapTask:负责 Map 阶段的整个数据处理流程。  
3. ReduceTask:负责 Reduce 阶段的整个数据处理流程。  

## 5.常用数据序列化类型

|Java 类型 |Hadoop Writable 类型 |
|---|---|
|**`Boolean`** |BooleanWritable|
|**`Byte`** |ByteWritable|
|**`Int`** |IntWritable|
|**`Float`** |FloatWritable|
|**`Long`** |LongWritable|
|**`Double`** |DoubleWritable|
|**`String`** |`Text`|
|**`Map`** |MapWritable|
|**`Array`** |ArrayWritable|
|**`Null`** |NullWritable|

## 6.MapReduce 编程规范

用户编写的程序分成三个部分:Mapper、Reducer 和 Driver

1. Mapper阶段  
  (1) 用户自定义的Mapper要继承自己的父类  
  (2) Mapper的输入数据是KV对的形式(KV的类型可自定义)  
  (3) Mapper中的业务逻辑写在map()方法中  
  (4) Mapper的输出数据是KV对的形式(KV的类型可自定义)  
  (5) map()方法(MapTask进程)对每一个<K,V>调用一次  
2. Reducer阶段  
  (1) 用户自定义的Reducer要继承自己的父类  
  (2) Reducer的输入数据类型对应Mapper的输出数据类型，也是KV  
  (3) Reducer的业务逻辑写在reduce()方法中  
  (4) ReduceTask进程对每一组相同k的<k,v>组调用一次reduce()方法  
3. Driver阶段  
  相当于YARN集群的客户端，用于提交我们整个程序到YARN集群，提交的是 封装了MapReduce程序相关运行参数的job对象

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
