---
author: "li_mingxie"
title: "【dns笔记】dns_基础配置resolv.conf(1)"
date: 2922-09-04T23:28:49+08:00
tags: [
    "network",
    "dns",
]
categories: [
    "network",
    "dns",
]
---

这一篇更加详细的整理了一些dns相关的内容。  <!--more-->

## resolv.conf配置

Linux是 /etc/resolv.conf文件可以配置DNS相关信息。  

**例子**  

```bash
$cat /etc/resolv.conf
#定义本地域名
domain  limingxie.com
#定义域名的搜索列表
search  www.limingxie.com  limingxie.com
#定义DNS服务器的IP地址
nameserver 219.141.136.10
nameserver 219.141.140.10
```

其中nameserver是必填，没有指定nameserver就找不到DNS服务。  

#### nameserver

表明DNS服务器的IP地址。可以有很多行的nameserver，每一个带一个IP地址。  
查询时就按nameserver在本文件中的顺序进行，且只有当第一个nameserver没有反应时才查询下面的nameserver。  

#### domain

声明主机的域名，很多程序用到它。  
如邮件系统；当为没有域名的主机进行DNS查询时，也要用到。  
如果没有域名，主机名将被使用，删除所有在第一个点( .)前面的内容。  

#### search

它的多个参数指明域名查询顺序。当要查询没有域名的主机，主机将在由search声明的域中分别查找。  
domain和search不能共存；如果同时存在，后面出现的将会被使用。  

#### sortlist

允许将得到域名结果进行特定的排序。它的参数为网络/掩码对，允许任意的排列顺序。  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
