---
author: "li_mingxie"
title: "【zookeeper笔记】zookeeper_znode结构和操作(2)"
date: 2022-08-12T23:28:49+08:00
tags: [
    "zookeeper",
]
categories: [
    "zookeeper",
]
---

这一篇简单的整理了znode结构和操作相关内容。  <!--more-->

## 1.znode结构

zookeeper中的存储数据是由节点znode组成，以key/value形式存储数据。  
整体结构类似于linux文件系统的模式以树形结构存储。根路径以 / 开头。

```bash
/
|___zookeeper
|___ceshi
  |___zhangsan
  |___lisi
... ...
```

## 2.znode相关操作

需要在zookeeper的bin目录，通过客户端zkCli.sh连接zookeeper服务。  

```
#打开客户端
root@zoo1:/apache-zookeeper-3.8.0-bin/bin# zkCli.sh
... ...

#查看信息
[zk: localhost:2181(CONNECTED) 0] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 1] ls /zookeeper
[config, quota]
[zk: localhost:2181(CONNECTED) 2] ls /zookeeper/config
[]
[zk: localhost:2181(CONNECTED) 3] ls /zookeeper/quota
[]
```

#### 2.1 创建

```bash
#创建持久节点
[zk: localhost:2181(CONNECTED) 9] create /ceshi zhangsan
Created /ceshi

#创建临时节点
[zk: localhost:2181(CONNECTED) 31] create -e /linshi
Created /linshi

#创建有序节点
[zk: localhost:2181(CONNECTED) 32] create -s /xuhao
Created /xuhao0000000007
```

#### 2.2 查看

```bash
#查看
[zk: localhost:2181(CONNECTED) 10] get /ceshi
zhangsan

#查看监听，只要做一次监听，下次还需要做。
[zk: localhost:2181(CONNECTED) 36] get -w /ceshi
xiaoming

#查看节点详细
[zk: localhost:2181(CONNECTED) 35] get -s /ceshi
xiaoming
cZxid = 0x300000011
ctime = Thu Aug 18 02:39:00 UTC 2022
mZxid = 0x300000016
mtime = Thu Aug 18 02:52:36 UTC 2022
pZxid = 0x300000011
cversion = 0
dataVersion = 4
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 8
numChildren = 0
```

* cZxid 创建节点时的事务ID
* ctime 创建节点时的时间
* mZxid 最后修改节点时的事务ID
* mtime 最后修改节点时的时间
* pZxid 表示该节点的子节点列表最后一次修改的事务ID，添加子节点或删除子节点就会影响子节点列表，但是修改子节点的数据内容则不影响该ID(**注意，只有子节点列表变更了才会变更pzxid，子节点内容变更不会影响pzxid**)
* cversion 子节点版本号，子节点每次修改版本号加1
* dataversion 数据版本号，数据每次修改该版本号加1
* aclversion 权限版本号，权限每次修改该版本号加1
* ephemeralOwner 创建该临时节点的会话的sessionID。（**如果该节点是持久节点，那么这个属性值为0**）
* dataLength 该节点的数据长度
* numChildren 该节点拥有子节点的数量（**只统计直接子节点的数量**）

#### 2.3 修改和删除

```bash
#修改
[zk: localhost:2181(CONNECTED) 11] set /ceshi xiaoming
[zk: localhost:2181(CONNECTED) 12] get /ceshi
xiaoming

#删除
[zk: localhost:2181(CONNECTED) 13] delete /ceshi
[zk: localhost:2181(CONNECTED) 14] ls /
[zookeeper]
```

#### 2.4 watch

第一步：客户端1

```bash
[zk: localhost:2181(CONNECTED) 27] get /ceshi
zhangsan
```

第二步：客户端2

```bash
#监听
[zk: localhost:2181(CONNECTED) 5] get -w /ceshi
zhangsan
```

第三步：客户端1

```bash
[zk: localhost:2181(CONNECTED) 28] set /ceshi xiaoming
```

第四步：客户端2

```bash
[zk: localhost:2181(CONNECTED) 6]
WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/ceshi
```

## 3.zkCli常用命令

* **help:** 显示所有操作命令  
* **ls path:** 查看当前znode中所包含的内容  
* **ls2 patch:** 查看当前节点数据并能看到更细次数等其他信息  
* **create:** 创建 -e 临时(重启或超时会消失) -s含有序列  
* **get:** 查看节点的值  
* **set:** 设置节点的值  
* **stat:** 查看状态  
* **delete:** 删除  
* **rmr:** 递归的删除节点  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
