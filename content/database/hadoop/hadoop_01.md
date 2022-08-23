---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_基础概念(1)"
date: 2022-08-14T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这篇文章整理了hadoop相关的基础概念。<!--more-->

## 1.Hadoop概述

Hadoop是一个由Apache基金会所开发的分布式系统基础架构。  
主要解决，`海量数据的存储`和`海量数据的分析计算`问题。  
广义上来说，Hadoop通常是指一个更广泛的概念(Hadoop生态圈)。  

**`官网地址`:** <http://hadoop.apache.org>

## 2.HDFS, MapReduce, YARN

Hadoop 主要包含 HDFS, MapReduce, YARN 三大组件。

* HDFS(数据存储)
* MapReduce(计算)
* YARN（资源调度）

#### 2.1 HDFS

Hadoop Distributed File System，简称 HDFS，是一个分布式文件系统。  
HDFS 的存储单位是块(Block)，一个文件可能会被分为多个块储存在物理存储器中。  
因此 HDFS 往往会按照设定者的要求把数据块复制n份并存储在不同的数据节点(储存数据的服务器)上  
如果一个数据节点发生故障数据也不会丢失。

> HDFS适合一次写入，多次读出的场景。一个文件经过创建、写入和关闭 之后就不需要改变。

**命名节点`NameNode(nn)`**:  

存储文件的元数据，如文件名，文件目录结构，文件属性(生成时间、副本数、文件权限)，以及每个文件的块列表和块所在的DataNode等。
是用于储存这些映射信息并提供映射服务的计算机，在整个HDFS系统中扮演"管理员"的角色，因此一个HDFS集群中只有一个命名节点。

**数据节点`DataNode(dn)`**:  

在本地文件系统存储文件块数据，以及块数据的校验和。是HDFS存储的基本单位。

**副命名节点`Secondary NameNode(2nn)`**:  

副命名节点 (Secondary NameNode) 别名"次命名节点"，是命名节点的"秘书"。  
它主要负责分摊命名节点的压力、备份命名节点的状态并执行一些管理工作，如果命名节点要求它这样做的话。  
如果命名节点坏掉了，它也可以提供备份数据以恢复命名节点。副命名节点可以有多个。  

#### 2.2 MapReduce

MapReduce 的含义就像它的名字一样浅显：Map 和 Reduce (映射和规约) 。  
MapReduce 将计算过程分为两个阶段:  

* Map 阶段并行处理输入数据  
* Reduce 阶段对 Map 结果进行汇总  

#### 2.3 YARN

Yet Another Resource Negotiator简称YARN，另一种资源协调者，是Hadoop的资源管理器。  

1)ResourceManager(RM):管理整个集群资源(内存、CPU等)  
2)NodeManager(NM):管理单个节点服务器资源  
3)ApplicationMaster(AM):管理单个任务运行  
4)Container:容器，相当一台独立的服务器，里面封装了任务运行所需要的资源，如内存、CPU、磁盘、网络等。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
