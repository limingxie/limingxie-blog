---
author: "li_mingxie"
title: "【redis笔记】redis_持久化(4)"
date: 2022-08-05T23:28:49+08:00
tags: [
    "redis",
]
categories: [
    "redis"
]
---

这一篇整理了redis持久化相关的内容。<!--more-->  
redis虽然是内存行数据库，但是他的数据是可以持久化的，  
当遇到特殊情况数据库宕机，可以通过持久化技术回复大部分的数据。

## 1.RDB（Redis DataBase）

在指定的时间间隔内将内存中的数据集 **`快照Snapshot`** 写入磁盘。  

在redis.conf中配置文件名称，默认为dump.rdb  

```redis
#rdb的保存的文件名
dbfilename dump.rdb

#rdb文件的保存路径
dir ./

#保存策略
save 900 1  //900秒内有一条数据变更
save 300 10 //300秒内有10条数据变更
save 60 10000 //60秒内有10000条数据变更

#手动保存快照
save    //不接受存储数据，阻塞性的保存。
bgsave  //fork()一个线程，非阻塞性保存
```

#### 关于fork

在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，  
出于效率考虑，Linux中引入了“写时复制技术”，一般情况父进程和子进程会共用同一段物理内存，  
只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程

其他配置项

```redis
#当Redis无法写入磁盘的话，直接关掉Redis的写操作
stop-writes-on-bgsave-error yes

#进行rdb保存时，将文件压缩
rdbcompression yes

#在存储快照后，还可以让Redis使用CRC64算法来进行数据校验，
#但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能
rdbchecksum yes
```

## 2.AOF（Append Of File）

以日志的形式来记录每个写操作  
AOF默认不开启，需要手动在配置文件中配置

```redis
#打开AOF
appendonly on

#配置文件名, AOF文件的保存路径，同RDB的路径一致
appendfilename "appendonly.aof"

#AOF同步频率设置
appendfsync everysec
```

> AOF和RDB同时开启，系统默认取AOF的数据

AOF是追加日志的方式存储数据的，所以过一些时间后文件会变得很大  
可以使用命令**`bgrewriteaof`**，只保留可恢复的数据。  

```redis
#遇到AOF文件损坏，可通过以下命令进行恢复
redis-check-aof  --fix  appendonly.aof   

##何时自动使用bgrewriteaof
auto-aof-rewrite-percentage 100 //当数据量多出(起初的)100%时
auto-aof-rewrite-min-size 64mb //当数据量超过64mb时
```

## 用哪种方式?

>官方推荐两个都启用。

* 如果对数据不敏感，可以选单独用RDB。
* 不建议单独用 AOF，因为可能会出现Bug。
* 如果只是做纯内存缓存，可以都不用。

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
