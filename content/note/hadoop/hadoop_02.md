---
author: "li_mingxie"
title: "【hadoop笔记】hadoop_HDFS的shell操作(2)"
date: 2022-08-15T23:28:49+08:00
tags: [
    "hadoop",
]
categories: [
    "hadoop",
]
---

这篇文章整理了HDFS的shell操作相关的内容。<!--more-->

## 1.基本语法

```bash
hadoop fs 具体命令 
OR 
hdfs dfs 具体命令
```

```bash
$ bin/hadoop fs
[-appendToFile <localsrc> ... <dst>]
       [-cat [-ignoreCrc] <src> ...]
       [-chgrp [-R] GROUP PATH...]
       [-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
       [-chown [-R] [OWNER][:[GROUP]] PATH...]
       [-copyFromLocal [-f] [-p] <localsrc> ... <dst>]
       [-copyToLocal [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
       [-count [-q] <path> ...]
       [-cp [-f] [-p] <src> ... <dst>]
       [-df [-h] [<path> ...]]
[-du [-s] [-h] <path> ...]
       [-get [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
       [-getmerge [-nl] <src> <localdst>]
       [-help [cmd ...]]
       [-ls [-d] [-h] [-R] [<path> ...]]
       [-mkdir [-p] <path> ...]
       [-moveFromLocal <localsrc> ... <dst>]
       [-moveToLocal <src> <localdst>]
       [-mv <src> ... <dst>]
       [-put [-f] [-p] <localsrc> ... <dst>]
       [-rm [-f] [-r|-R] [-skipTrash] <src> ...]
       [-rmdir [--ignore-fail-on-non-empty] <dir> ...]
<acl_spec> <path>]]
       [-setrep [-R] [-w] <rep> <path> ...]
```

## 2.常用命令

#### 2.1 启动

启动 Hadoop 集群

```bash
sbin/start-dfs.sh
sbin/start-yarn.sh
```

-help:输出这个命令参数

```bash
hadoop fs -help rm
```

创建/sanguo 文件夹

```bash
hadoop fs -mkdir /sanguo
```

#### 2.2 上传

-moveFromLocal:从本地剪切粘贴到 HDFS

```bash
$ vim shuguo.txt
输入:
shuguo
$ hadoop fs -moveFromLocal ./shuguo.txt /sanguo
```

-copyFromLocal:从本地文件系统中拷贝文件到 HDFS 路径去

```bash
$ vim weiguo.txt
输入:
weiguo
$ hadoop fs -copyFromLocal weiguo.txt
/sanguo
```

-put:等同于 copyFromLocal，生产环境更习惯用 put

```bash
$ vim wuguo.txt
输入:
wuguo
$ hadoop fs -put ./wuguo.txt /sanguo
```

-appendToFile:追加一个文件到已经存在的文件末尾

```bash
$ vim liubei.txt
输入:
liubei
$ hadoop fs -appendToFile liubei.txt
/sanguo/shuguo.txt
```

#### 2.3 下载

-copyToLocal:从 HDFS 拷贝到本地

```bash
$ hadoop fs -copyToLocal
/sanguo/shuguo.txt ./
```

-get:等同于 copyToLocal，生产环境更习惯用 get

```bash
$ hadoop fs -get
/sanguo/shuguo.txt ./shuguo2.txt
```

#### 2.4 HDFS 操作

-ls: 显示目录信息  
-cat:显示文件内容  
-chgrp、-chmod、-chown:Linux 文件系统中的用法一样，修改文件所属权限  
-mkdir:创建路径  
-cp:从 HDFS 的一个路径拷贝到 HDFS 的另一个路径  
-mv:在 HDFS 目录中移动文件  
-tail:显示一个文件的末尾 1kb 的数据  
-rm:删除文件或文件夹  
-rm -r:递归删除目录及目录里面内容  
-du 统计文件夹的大小信息

```bash
$ hadoop fs -du -s -h /jinguo
27 81 /jinguo
$ hadoop fs -du -h /jinguo 14 42 /jinguo/shuguo.txt
7 21 /jinguo/weiguo.txt
6 18 /jinguo/wuguo.tx
```

-setrep:设置 HDFS 中文件的副本数量

```bash
hadoop fs -setrep 10 /jinguo/shuguo.txt
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
