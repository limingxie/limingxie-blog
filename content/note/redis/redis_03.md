---
author: "li_mingxie"
title: "【redis笔记】redis_事务(3)"
date: 2022-08-04T23:28:49+08:00
tags: [
    "redis",
]
categories: [
    "redis"
]
---

这一篇整理了redis事务相关的内容。<!--more-->  

## 1.multi、exec、discard

```redis
#添加数据
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> set k2 v2
OK
127.0.0.1:6379> set k3 v3
OK

#开启事务
127.0.0.1:6379> multi
OK
127.0.0.1:6379(TX)> get k1
QUEUED
127.0.0.1:6379(TX)> set k2 v22
QUEUED
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> get v2
QUEUED
127.0.0.1:6379(TX)> exec  #或 discard 撤销
1) "v1"
2) OK
3) OK
4) (nil)
```

> multi是打包命令的过程, exec执行打包好的命令。  
> 其中有错误的命令, 其它命令都有效，并非遵守原子性。

## 2.watch, unwatch

用于监视一个(或多个)key，如果在事务执行之前这个(或这些)key被其他命令所改动，事务将被打断。

```redis
WATCH key [key ...]
```

```
127.0.0.1:6379> watch k1
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379(TX)> set k2 v22
QUEUED
127.0.0.1:6379(TX)> get k1  //这一过程中在其他线程修改k1的值
QUEUED
127.0.0.1:6379(TX)> exec
(nil)                       //结果直接为nil
```

> watch后 exec， discard，unwatch 都会清理watch的信息。

## 3.LUA脚本

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，  
减少反复连接redis的次数。提升性能。

redis的lua脚本功能，只有在2.6以上的版本才可以使用。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

<!-- 使用工具ab
CentOS6 默认安装
CentOS7需要手动安装

1、联网：yum install httpd-tools -->
