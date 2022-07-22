---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_系统管理(04)"
date: 2022-07-20T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了系统管理相关的内容。<!--more-->  

## 1.进程和服务

计算机中，一个正在执行的程序或命令，被叫做“进程”(process)。  
启动之后一只存在、常驻内存的进程，一般被称作“服务”(service)。  

**ifconfig**:network interfaces configuring 网络接口配置  
**ping 目的主机**: 测试当前服务器是否可以连接目的主机

## 2.systemctl

```bash
#基本用法
systemctl start | stop | restart | status 服务名

#查看服务的方法:
/usr/lib/systemd/system

#CentOS6中的iptable=> firewalld
systemctl status firewalld
systemctl start | stop | restart firewalld
```

## 3.设置后台服务的自启配置

```bash
#查看服务开机启动状态
systemctl list-unit-files 

#关掉指定服务的自动启动
systemctl disable service_name 

#开启指定服务的自动启动
systemctl enable service_name
```

## 4.运行级别

```bash
#查看当前运行界别
systemctl get-default

#修改当前运行级别
systemctl set-default TARGET.target
```

multi-user.target 等价于原运行级别 3(多用户有网，无图形界面)  
graphical.target 等价于原运行级别 5(多用户有网，有图形界面)  

[图片备用地址](https://limingxie.github.io/images/system/linux/runlevel_01.png)  
![runlevel_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/linux/runlevel_01.png)

## 5.防火墙

```bash
#查看防火墙状态
systemctl status firewalld

#临时关闭防火墙
systemctl stop firewalld

#设置开机时开启防火墙
systemctl enable firewalld.service

#设置开机时关闭防火墙
systemctl disable firewalld.service
```

## 6.关机或重启

#### (1)sync

将数据由内存同步到硬盘中

#### (2)halt

停机，关闭系统，但不断电

#### (3)poweroff

关机，断电

#### (4)reboot

就是重启，等同于 shutdown -r now

#### (5)shutdown

* -H: --halt, 停机
* -r: reboot, 重启
* now: 立刻关机
* 时间: 等待多久后关机(时间单位是分钟)。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
