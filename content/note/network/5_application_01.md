---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)简介(1)"
date: 2022-04-18T18:05:49+08:00
tags: [
    "OSI",
    "DNS",
    "DHCP",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了应用层的 域名，DNS, DHCP。
<!--more-->
## 1.应用层常见协议

* 超文本传输协议：HTTP、HTTPS
* 文件传输：FTP
* 电子邮件：SMTP、POP3、IMAP
* 动态主机配置：DHCP
* 域名系统：DNS

### 1.1 域名（Domain Name）

由于IP地址不方便记忆，并且不能表达组织的名称和性质，人们设计出了域名（比如baidu.com）。  
但实际上，为了能够访问到具体的主机，最终还是得知道目标主机的IP地址。  

为什么不直接用域名？  
=> IP地址固定4个字节，域名随随便便都至少10几个字节，这无疑会增加路由器的负担，浪费流量。  

根据级别不同，域名可以分为：  

**顶级域名:**（Top-level Domain，简称TLD。例，www.baidu.com）  
**二级域名:**（例，graph.baidu.com）  
**三级域名:**（例，**）  
… …

#### 1.1.1 顶级域名的分类

1. 通用顶级域名（General Top-level Domain，简称gTLD）  
**=>** .com（公司），net（网络结构），.org（组织结构，公益性居多），.edu（教育），.gov（政府部门），.int（国际组织）等  
2. 国家及地区顶级域名（Country Code Top-level Domain，简称ccTLD）  
**=>** .cn（中国），.jp（日本），.uk（英国）  
3. 新通用顶级域名（New Generic Top-level Domain，简称New gTLD）  
**=>** .vip，.xyz，.top，.club，.shop等  

#### 1.1.2 二级域名  

二级域名是指顶级域名之下的域名。  

在通用顶级域名下，它一般指域名注册人的名称，例如google，baidu，microsoft等  
在国家及地区顶级域名下，它一般指注册类别的，例如com，edu，gov，net等  

[图片备用地址](https://limingxie.github.io/images/network/application/application_01.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_01.png?x-oss-process=image/resize,w_700,m_lfit)  

例如baidu.com，其实完整的写法是www.baidu.com.，  
最后是有一个.的，这个点就代表根服务器，DNS解析的时候会自动加上去。  

### 1.2 DNS（Domain Name System）

DNS英文译为：域名系统。  

利用DNS协议，可以将域名（比如baidu.com）解析对应的IP地址（比如220.180.38.120）。  
DNS可以基于UDP协议，也可以基于TCP协议，服务器占用53端口。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_02.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_02.png?x-oss-process=image/resize,w_700,m_lfit)  

设备访问一个域名的时候会优先从本地名称服务器查找对应的IP，如果没有找到就会请求根服务器，一级一级往下找，直到找到对应的域名为止，  
最后将域名对应的IP返回到设备并在本地服务器做一个缓存。  

常用命令：  
**dscacheutil -flushcache**：清空DNS缓存记录  
**ping 域名**：测试指定域名服务器的连接状态  
**nslookup 域名**：查看指定域名的IP地址  

## 2.IP地址的分配

IP地址按照分配方式可以分为：静态IP地址、动态IP地址。  

### 2.1 静态IP地址

静态IP地址是手动设置的。  
适用场景：不怎么挪动的台式机（比如学校机房中的台式机），服务器等。  

### 2.2 动态IP地址（DHCP）

从DHCP服务器自动获取IP地址。  
适用场景：移动设备、无线设备等。  
**DHCP（Dynamic Host Configuration Protocol）**，动态主机配置协议。  
DHCP协议基于UDP协议，客户端是68端口，服务器是67端口。  
DHCP服务器会从IP地址池中，挑选一个IP地址“出租”给客户端一段时间，时间到期就回收它们。平时家里上网的路由器就可以充当DHCP服务器。  

#### 2.2.1 分配IP地址的4个阶段

**DISCOVER**：发现服务器。  
发广播包（源IP是0.0.0.0，目标IP是255.255.255.255，目标MAC是FF:FF:FF:FF:FF:FF）  
**OFFER**：提供租约  
服务器返回可以租用的IP地址，以及租用期限、子网掩码、网关、DNS等信息  
**REQUEST**：选择IP地址  
客户端选择一个OFFER，发送广播包进行回应  
**ACKNOWLEDGE**：确认  
被选中的服务器发送ACK数据包给客户端  

[图片备用地址](https://limingxie.github.io/images/network/application/application_03.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_03.png?x-oss-process=image/resize,w_400,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_04.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_04.png?x-oss-process=image/resize,w_700,m_lfit)  

DHCP服务器可以跨网段分配IP地址么？（DHCP服务器、客户端不在同一个网段）  
可以借助DHCP中继代理（DHCP Relay Agent）实现跨网段分配IP地址。  

#### 2.2.2 自动续约

客户端会在租期不足的时候，自动向DHCP服务器发送REQUEST信息申请续约。  

常用命令：  
ifconfig en0  
ipconfig getpacket en0  
ipconfig set en0 DHCP：重新申请IP地址、申请续约（延长租期）  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
