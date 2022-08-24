---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_HDFS结构简介(3)"
date: 2022-08-16T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这篇文章整理了HDFS结构相关的内容。<!--more-->

----------------------------------------------

## 1.HDFS 写数据流程

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_01.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_01.png)

----------------------------------------------

## 2.节点距离计算

**`节点距离`**:两个节点到达最近的共同祖先的距离总和。

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_02.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_02.png)

----------------------------------------------

## 3.副本节点选择

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_03.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_03.png)

----------------------------------------------

## 4.HDFS 读数据流程

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_04.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_04.png)

----------------------------------------------

## 5.NameNode工作机制

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_05.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_05.png)

#### CheckPoint时间设置

1)通常情况下，SecondaryNameNode 每隔一小时执行一次。
[hdfs-default.xml]

```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>3600s</value>
</property>
```

2)一分钟检查一次操作次数，当操作次数达到 1 百万时，SecondaryNameNode 执行一次。

```xml
<property>
  <name>dfs.namenode.checkpoint.txns</name>
  <value>1000000</value> 
  <description>操作动作次数</description> 
</property>
<property>
  <name>dfs.namenode.checkpoint.check.period</name>
  <value>60s</value>
  <description> 1 分钟检查一次操作次数</description> 
</property>
```

----------------------------------------------

## 6.DataNode工作机制

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_06.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_06.png)

DN 向 NN 汇报当前解读信息的时间间隔，默认 6 小时;

```xml
<property>
    <name>dfs.blockreport.intervalMsec</name>
    <value>21600000</value>
    <description>Determines block reporting interval in milliseconds.</description>
</property>
```

DN 扫描自己节点块信息列表的时间，默认 6 小时

```xml
<property>
    <name>dfs.datanode.directoryscan.interval</name>
    <value>21600s</value>
    <description>Interval in seconds for Datanode to scan data directories and reconcile the difference between blocks in memory and on the disk.
    Support multiple time unit suffix(case insensitive), as described
    in dfs.heartbeat.interval.
    </description>
</property>
```

----------------------------------------------

## 7.数据完整性

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_07.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_07.png)

----------------------------------------------

## 8.掉线时限参数设置

[图片备用地址](https://limingxie.github.io/images/database/hadoop/hadoop_08.png)  
![hadoop_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/hadoop_08.png)

hdfs-site.xml 配置文件中的  
heartbeat.recheck.interval 的单位为毫秒，  
dfs.heartbeat.interval 的单位为秒。

```xml
<property>
   <name>dfs.namenode.heartbeat.recheck-interval</name>
   <value>300000</value>
</property>
<property>
   <name>dfs.heartbeat.interval</name>
   <value>3</value>
</property>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
