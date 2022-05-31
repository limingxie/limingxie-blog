---
author: "li_mingxie"
title: "【网络协议笔记】网络基础整理"
date: 2022-04-09T16:27:49+08:00
tags: [
    "network",
    "internet",
    "server room",
    "LAN",
    "MAN",
    "WAN",
    "IP",
    "NAT",
]
categories: [
    "network",
]
---

有一些些网络基础的概念，知道了能会更好的理解网络。

## 1.网络，互联网，因特网

#### 1.1 网络(Network)

网络是把同一个网段的一些设备链接起来就能形成网络。  
如下图：  

[图片备用地址](https://limingxie.github.io/images/network/internet_definition/network.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/internet_definition/network.png?x-oss-process=image/resize,w_300,m_lfit)

#### 1.2 互联网(internet)

互联网是把不用网段的一些设备通过路由和交换机链接起来的叫互联网。  
如下图：

[图片备用地址](https://limingxie.github.io/images/network/internet_definition/internet.png)  
![internet](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/internet_definition/internet.png?x-oss-process=image/resize,w_500,m_lfit)

#### 1.3 因特网(Internet)

全球最大的互联网叫因特网，我们所说的上外网是指因特网。  

## 2.ISP

ISP是Internet Service Provider，是指网络运营商。  
比如移动，电信，网通，铁通等等。

**PC -> ISP -> Internet**

## 3.服务器机房(Server Room)

链接或下载不同的ISP厂商提供的资源，因服务器机房不同速度也会不同。如图：  

[图片备用地址](https://limingxie.github.io/images/network/internet_definition/server_room.png)  
![server_room](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/internet_definition/server_room.png)

## 4.网络分段

#### 4.1 局域网(Local Area Network, LAN)

局域网是指在一定区域内由多台计算机互联而成的计算机群。  
比如在公司，学校，家庭，一幢大楼等等... ...  
局域网中使用最广泛的网络技术叫**以太网(Ethernet)**。  
我们手机上常见到的**WALN(Wireless Local Area Network)**，无线局域网。  

#### 4.2 城域网(Metropolitan Area Network, MAN)

城域网是一种能覆盖一个城市范围的广域网。  

#### 4.3 广域网(Wide Area Network, WAN)

广域网是连接不同地区局域网或城域网，通常跨越很大的物理范围。  
从几十公里到几千公里，它能连接多个地区、城市和国家，或横跨几个洲并能提供远距离的通信。  
广域网的核心是广域专线，广域专线业务大多是面向企业。企业可以租用ISP的专线来提供跨域的服。务。  

## 5.常见的几种接口

FastEthernet(快速以太网口 100M)  
GigabitEth(千兆以太网口)  
以上网口是电脑和电脑或交换机之间链接时会用到。

Serial(串口)  
以上网口是路由和路由之间链接的时会用到。  

## 6.上网方式

1. 电话线入户:  
ADSL(Asynchronous Digital Subscriber Line)  
猫(modem): 调制解调器,进行数字信号和模拟信号的转换。  
非对称数字用户线路，提供上下行速率不同的数据传输。  

2. 光纤入户:  
光猫(Optical Modem): 光调制解调器,进行数字信号和光信号的转换。

3. 网线入口:

**家用无线路由器的结构**  

[图片备用地址](https://limingxie.github.io/images/network/internet_definition/home_router.png)  
![home_router](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/internet_definition/home_router.png?x-oss-process=image/resize,w_600,m_lfit)

## 7.公网IP, 私网IP

IP可以分为公网IP和私网IP。

#### 7.1 公网IP(Public IP)

公网IP是由因特网信息中心(Internet Network Information Center, Inter NIC)统一分配和管理。  
公网的IP是ISP向Inter NIC(Internet Network Interface)申请公网IP。  
因特网(Internet)的路由器只保存到达公网的路由表，没有到达私网的路由表。  

#### 7.2 私网IP(Private IP)

主要用于局域网，下面是保留的私网IP  
A类: 10.0.00/8  1个A类网络  
B类: 172.16.0.0/16 ~ 173.31.0.0/16  16个B类网络  
C类: 192.168.0.0/24 ~ 192.168.255.0/24  256个C类网络  

## 8.NAT(Network Address Translation)

NAT(Network Address Translation)，中文意思是“网络地址转换”，  
它是一种把私网IP地址转换成合法网络IP地址的技术。  

公网路由是只保存公网IP的路由表，所以只有公网IP才能访问公网。  
所以私网IP访问Internet时，需要NAT技术转换公网IP地址，才能连接到外网(因特网)。  
这样会隐藏内部真实IP, 但是想通过路由的痕迹查的话还是可以查出来的。  

下图是简单的描述了私网IP通过NAT技术连外网的过程：  
[图片备用地址](https://limingxie.github.io/images/network/internet_definition/NAT.png)  
![NAT](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/internet_definition/NAT.png?x-oss-process=image/resize,w_600,m_lfit)

**NAT分类:**

NAT技术可以分一下几种：

1. 静态转换
手动分配NAT映射表(一对一)

2. 动态转换
定义外部地址池，动态随机分配(一对一)

3. PAT(Port Address Translation)
采用端口多路复用方式，通过端口号标识不同的数据流的方式来实现NAT转换，  
这样就能实现多对一的转换，能节约公网IP资源，是目前最广泛的NAT实现方式。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

<!-- https://blog.csdn.net/yangbenben8866?type=blog -->