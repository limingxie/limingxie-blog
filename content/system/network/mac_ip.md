---
author: "li_mingxie"
title: "【网络协议笔记】mac地址和ip地址简介"
date: 2022-04-08T22:28:49+08:00
tags: [
    "ARP",
    "IP",
    "MAC",

]
categories: [
    "network",
]
---

## 1.MAC地址

每个网卡都有一个唯一(6个字节48bit)的MAC地址(Media Access Control Address)。  
这个地址是由硬件设备生成的，是网络设备的唯一标识。  
固化在网卡的ROM中，有IEEE802标准规定的MAC地址格式。<!--more-->

|组织唯一标识符|网络接口标识符|
|--|--|
|40-55-82|TA-BC-D6|

**当48位都是1，代表的是广播地址：FF-FF-FF-FF-FF-FF**

mac地址可以修改:当遇到限制MAC上网时，修改成白名单的MAC地址就能蹭网。

> MAC地址的获取：
> 当不知道对方的MAC地址时，可以通过发送ARP广播后去对方的MAC地址。  
> 通过ARP协议获取得对方MAC地址会动态的缓存到本机(默认2分钟)。

## 2.IP地址

IP地址(Internet Protocol Address): 互联网山的每个主机都有一个IP地址。  
目前有2个版本: IPv4版本(32bit 4个字节)，IPv6版本(128bit 16个字节)。  

#### 2.1 IP地址的组成

[图片备用地址](https://limingxie.github.io/images/network/ip/ip_address.png)  
![ip_address](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/ip_address.png)

#### 2.2 子网掩码subnet mask

我们查看IP会看到一下两种地址。  
IP地址: 192.168.1.10  
子网掩码: 255.255.255.0  

每一个IP地址都有一个子网掩码，每一个子网掩码都是一个32位的二进制数，每一位代表一个IP地址的每一位。  
子网掩码的作用是什么？ 用子网掩码来计算IP的【网段network segment】。  
我们知道两个设备的IP地址如果是在不同的网段，是不能通信的。  

先看下图:  
[图片备用地址](https://limingxie.github.io/images/network/ip/ip_01.png)  
![ip_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/ip_01.png?x-oss-process=image/resize,w_500,m_lfit)

图中第1行是ip地址，第二行是子网掩码，第三行是网段。  
网段是怎么计算出来的？子网掩码的数字是1，就取IP地址的值，子网掩码的数字是0，结果就是0。  
1111 1111 是 255。  

那上述的

```
IP地址: 192.168.1.10  
子网掩码: 255.255.255.0  
```

**网段是 192.168.1.0**

#### 2.3 IP地址分类

IP地址根据网络ID的不同分为5种类型，A类地址、B类地址、C类地址、D类地址和E类地址。  
下面看看这个几个分类是如何分的。

**A类地址:**
网络ID（8bit）必须是0开头。

[图片备用地址](https://limingxie.github.io/images/network/ip/a_ip.png)  
![a_ip](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/a_ip.png?x-oss-process=image/resize,w_900,m_lfit)

**B类地址:**
网络ID（16bit）必须是10开头。

[图片备用地址](https://limingxie.github.io/images/network/ip/b_ip.png)  
![b_ip](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/b_ip.png?x-oss-process=image/resize,w_900,m_lfit)

**C类地址:**
网络ID（24bit）必须是110开头。

[图片备用地址](https://limingxie.github.io/images/network/ip/c_ip.png)  
![c_ip](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/c_ip.png?x-oss-process=image/resize,w_900,m_lfit)

**D类地址:** 1110开头，多播地址。  
**E类地址:** 1111开头，为今后保留。  

#### 2.4 子网掩码的CIDR表示法

子网掩码CIDR(Classless Inter-Domain Routing)表示方法如下:  
192.168.1.100/24 => 代表子网掩码有24个1，也就是 255.255.255.0  
123.168.100.39/16 => 代表子网掩码有16个1，也就是 255.255.0.0  

那如果这样呢？  
123.168.100.39/15 => 代表子网掩码有15个1，也就是 255.254.0.0  

#### 2.5 子网subnetwork

IPv4用A类B类C类划分，会浪费很多资源。  
举例子我分到了B类地址，但是我只需要一半的资源，那剩下的一般是不是浪费了？  
为了防止这种现象发生，就需要用到子网。  

我们看看下列图:
[图片备用地址](https://limingxie.github.io/images/network/ip/subnetwork.png)  
![subnetwork](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/subnetwork.png?x-oss-process=image/resize,w_700,m_lfit)

> 我们在16字节IP地址的后面再加一个0 或 1 来区分B类地址，分别给其他的人。  
这样加0的 分给一个人。加1的 分给另一个人就行了。  
需要注意的是，这么区分的时候子网掩码的也需要有相对应调整，往后挪1位子网掩码也要多1个1。  
如果需要分成4段，按就再往后的2位字节来区分就行了。  

看看下图如何分4段:  
[图片备用地址](https://limingxie.github.io/images/network/ip/subnetwork_01.webp)  
![subnetwork](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/subnetwork_01.webp?x-oss-process=image/resize,w_900,m_lfit)

#### 2.6 超网supernetting  

那超网是什么呢？ 子网是网右边挪子网掩码，那超网是往左边挪。  
看下图:  
[图片备用地址](https://limingxie.github.io/images/network/ip/supernetting.png)  
![supernetting](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/ip/supernetting.png?x-oss-process=image/resize,w_600,m_lfit)

> 那我们如何是区分超网和子网呢？  
> 很简单，先看是IP地址类型，再看子网掩码是往那边挪的。  
> 举例子: 192.168.1.10/25 就是子网，192.168.1.10/23 就是超网。  

## 2.7 练习

计算机A: 192.168.0.10/24
计算机B: 192.168.10.10/16

直链(交叉线)能通信吗？

我们先算一算两个计算机的网段:  
计算机A的网段 => 192.168.0.0  
计算机B的网段 => 192.168.0.0  

看网段相同，貌似可以通信，但是计算机A给计算机B发通信的时候会先判断是不是在同一个网段里。  
这时计算机A是没有计算机B的子网掩码，所以用自己的子网掩码来计算计算机B的网段后，计算出结果是网段不一样，所以发不出通信。  
虽然计算机B是可以发送给计算机A，但是计算机A也不能发送给计算机B。所以他两是无法通讯的。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
