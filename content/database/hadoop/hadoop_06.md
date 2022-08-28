---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_MapReduce压缩(6)"
date: 2022-08-19T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这篇文章整理了MapReduce过程中压缩相关的内容。<!--more-->

## 1.概述

#### 1.1 压缩的好处和坏处

* 压缩的优点:以减少磁盘 IO、减少磁盘存储空间。
* 压缩的缺点:增加 CPU 开销。

#### 1.2 压缩原则

(1)运算密集型的 Job，少用压缩  
(2)IO 密集型的 Job，多用压缩  

## 2.MR 支持的压缩编码

#### 2.1 压缩算法对比介绍

|压缩格式|Hadoop 自带?|算法|文件扩展名|是否可以切片|换成压缩格式后，原来的程序是否需要修改|
|---|---|---|---|---|---|
|DEFLATE|是，直接使用|DEFLATE|.deflate|否|和文本处理一样，不需要修改|
|Gzip|是，直接使用|DEFLATE|.gz|否|和文本处理一样，不需要修改|
|bzip2|是，直接使用|bzip2|.bz2|`是`|和文本处理一样，不需要修改|
|LZO|`否，需要安装`|LZO|.lzo|`是`|`需要建索引，还需要指定输入格式`|
|Snappy|是，直接使用|Snappy|.snappy|否|和文本处理一样，不需要修改|

#### 2.2 压缩性能的比较

|压缩算法|原始文件大小|压缩文件大小|压缩速度|
|---|---|---|---|
|gzip|8.3GB|1.8GB|17.5MB/s|58MB/s|
|bzip2|8.3GB|1.1GB|2.4MB/s|9.5MB/s
|LZO|8.3GB|2.9GB|49.3MB/s|74.6MB/s|

<http://google.github.io/snappy/>
snappy 压缩速度250MB/s 解压速度 500MB/s 压缩率一般

## 3.压缩方式选择

压缩方式选择时重点考虑:压缩/解压缩速度、压缩率(压缩后存储大小)、压缩后是否可以支持切片。

#### 3.1 Gzip 压缩

优点:压缩率比较高;  
缺点:不支持 Split;压缩/解压速度一般;  

#### 3.2 Bzip2 压缩

优点:压缩率高;支持 Split  
缺点:压缩/解压速度慢。  

#### 3.3 Lzo 压缩

优点:压缩/解压速度比较快;支持 Split;  
缺点:压缩率一般;想支持切片需要额外创建索引。  

#### 3.4 Snappy 压缩

优点:压缩和解压缩速度快;  
缺点:不支持 Split;压缩率一般;  

## 4 压缩位置选择

压缩可以在 MapReduce 作用的任意阶段启用。

[图片备用地址](https://limingxie.github.io/images/database/hadoop/map_reduce_10.png)  
![map_reduce_10](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/hadoop/map_reduce_10.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
