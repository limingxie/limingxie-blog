---
author: "li_mingxie"
title: "【dns笔记】dns_coredns简介(2)"
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

* /etc/hosts => 本地主机数据库
* /etc/host.conf => 解析配置文件
* /etc/resolv.conf => 解析配置文件

## 1.host

hosts —— the static table lookup for host name（主机名查询静态表）  

```bash
127.0.0.1 localhost.localdomain localhost
192.168.1.100 test001.com test001
192.168.1.120 ftpserver ftp120
```

优先级 ： dns缓存 > hosts > dns服务  

## 2.host.conf

/etc/host.conf 包含着解析库声明的配置信息  
系统识别的关键字有: order, trim, multi, nospoof和 reorder  

**order**  
这个关键字确定了主机查询是如何执行的. 它后面应该跟随一个或者更多的查询方式, 这些查询方式用逗号分隔.  
有效的方式有: bind, hosts和 nis.  

**trim**  
这个关键字可以多次出现. 每次出现其后应该跟随单个的以句点开头的域名.  
如果设置了它, resolv+ 库会自动截去任何通过 DNS 解析出来的主机名后面的域名.  
这个选项用于本地主机和域. (相关信息: trim 对于通过 NIS 或者 hosts 文件获取的主机名无效. 需要注意的是要确保在 hosts 文件中的每条记录的第一个主机名是全名或者非全名, 以适合于本地安装.)  

**multi**  
有效的值为: on和 off. 如果设置为 on, resolv+ 库会返回一台主机在 /etc/hosts 文件中出现的的所有有效地址,
而不只是第一个. 默认情况下设为 off , 否则可能会导致拥有庞大 hosts 文件的站点潜在的性能损失.  

**nospoof**  
有效的值为: on 和 off. 如果设置为 on, resolv+ 库会尝试阻止主机名欺骗以提高使用 rlogin 和 rsh 的安全性.
它是如下这样工作的: 在执行了一个主机地址的查询之后, resolv+ 会对该地址执行一次主机名的查询. 如果两者不匹配, 查询即失败.  

**spoofalert**  
如果该选项设为 on 同时也设置了 nospoof 选项, resolv+ 会通过 syslog 设施记录错误报警信息. 默认的值为 off.  

**reorder**  
有效的值为 on 和 off. 如果设置为 on, resolv+ 会试图重新排列主机地址, 以便执行 gethostbyname(3) 时,
首先列出本地地址(即在同一子网中的地址). 重新排序适合于所有查询方式. 默认的值为 off.  

## 3.resolv.conf

Linux是 /etc/resolv.conf文件可以配置DNS相关信息。  

**例子**  

```bash
$cat /etc/resolv.conf
#定义本地域名
domain  limingxie.com
#定义域名的搜索列表
search  www.limingxie.com  limingxie.com
#domain和search不能共存；如果同时存在，后面出现的将会被使用。
#定义DNS服务器的IP地址
nameserver 219.141.136.10
nameserver 219.141.140.10
```

其中nameserver是必填，没有指定nameserver就找不到DNS服务。  

#### 3.1 nameserver

表明DNS服务器的IP地址。可以有很多行的nameserver，每一个带一个IP地址。  
查询时就按nameserver在本文件中的顺序进行，且只有当第一个nameserver没有反应时才查询下面的nameserver。  

#### 3.2 domain

声明主机的域名，很多程序用到它。  
如邮件系统；当为没有域名的主机进行DNS查询时，也要用到。  
如果没有域名，主机名将被使用，删除所有在第一个点(.)前面的内容。  

#### 3.3 search

它的多个参数指明域名查询顺序。当要查询没有域名的主机，主机将在由search声明的域中分别查找。  
domain和search不能共存；如果同时存在，后面出现的将会被使用。  

#### 3.4 sortlist

允许将得到域名结果进行特定的排序。它的参数为网络/掩码对，允许任意的排列顺序。  

## nslookup

```bash
$ nslookup baidu.com
Server:  219.141.136.10  # => 通过 /etc/resolv.conf 配置的 nameserver 解析域名
Address: 219.141.136.10#53

Non-authoritative answer:
Name: baidu.com
Address: 39.156.66.10
Name: baidu.com
Address: 110.242.68.66
```

## dig

```bash
$ dig baidu.com

; <<>> DiG 9.10.6 <<>> baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 20678
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;baidu.com.   IN A

;; ANSWER SECTION:
baidu.com.  335 IN A 110.242.68.66
baidu.com.  335 IN A 39.156.66.10

;; Query time: 33 msec
;; SERVER: 219.141.136.10#53(219.141.136.10)  #=> 通过 /etc/resolv.conf 配置的 nameserver 解析域名
;; WHEN: Tue Sep 06 17:13:38 CST 2022
;; MSG SIZE  rcvd: 70
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
