---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_网络配置(03)"
date: 2022-07-19T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了网络配置。<!--more-->  

#### 1.ifconfig, ping

**ifconfig**:network interfaces configuring 网络接口配置  
**ping 目的主机**: 测试当前服务器是否可以连接目的主机

#### 2.IP 地址

查看ip配置文件

```bash
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

```bash
... ... 
BOOTPROTO="static" #IP 的配置方法[none|static|bootp|dhcp](引导时不 使用协议|静态分配IP|BOOTP协议|DHCP协议)
... ... 
UUID="e83804c1-3257-4584-81bb-660665ac22f6" #随机 id DEVICE="ens33" #接口名(设备,网卡)
ONBOOT="yes" #系统启动的时候网络接口是否有效(yes/no)
#IP 地址 IPADDR=192.168.1.100
#网关
GATEWAY=192.168.1.2
#域名解析器
DNS1=192.168.1.2
```

#### 3.重启网络

```bash
service network restart
```

#### 4.hostname

```bash
#查看 hostname
hostname

#修改hostname 需重启
vi /etc/hostname
```

#### 5.hosts 映射文件

```bash
#查看hosts
vim /etc/hosts
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
