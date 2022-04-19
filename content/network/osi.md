---
author: "li_mingxie"
title: "【网络协议笔记】浅谈OSI网络七层协议"
date: 2022-04-18T22:28:49+08:00
tags: [
    "OSI",
    "network",
    "protocal",
]
categories: [
    "network",
]
---

开放式系统互联模型（英语：Open System Interconnection Model，缩写：OSI；简称为OSI模型）是一种概念模型。  
从分布式应用程序数据的最高层表示到跨通信介质传输数据的物理实现。  
每个中间层为其上一层提供功能，其自身功能则由其下一层提供。功能的类别通过标准的通信协议在软件中实现。  
OSI模型分为七个层次。它们由低到高分别是物理层(PH)、数据链路层(DL)、网络层(N)、传输层(T)、会话层(S)、表示层(P)、应用层(A)。  

```
7层模型是偏理论的模型。实际落地的时，大部分都准守4层的TCP/IP协议族。  
因物理层和数据链路层比较重要，所以研究和学习时，有可能分5层结构。  
```

如下图:
[图片备用地址](https://limingxie.github.io/images/network/osi/OSI.jpg)  
![OSI](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/osi/OSI.jpg)

## 每层作用

看看维基百科是如何解释每层的作用：  
[维基百科:OSI协议](https://zh.wikipedia.org/wiki/OSI%E6%A8%A1%E5%9E%8B)

#### 7.应用层

应用层（Application Layer）提供为应用软件而设计的接口，以设置与另一应用软件之间的通信。  
例如：HTTP、HTTPS、FTP、Telnet、SSH、SMTP、POP3等。  

#### 6.表示层

表示层（Presentation Layer）把数据转换为能与接收者的系统格式兼容并适合传输的格式。  
如文本文件的ASCII格式和UTF-8格式。  

#### 5.会话层  

会话层（Session Layer）负责在数据传输中设置和维护计算机网络中两台计算机之间的通信连接。  
示例：RPC，SQL等。  

#### 4.传输层

传输层（Transport Layer）把传输表头（TH）加至资料以形成分组。传输表头包含了所使用的协议等发送信息。  
示例：TCP，UDP，SPX。  

#### 3.网络层

网络层（Network Layer）决定数据的路径选择和转寄，将网络表头（NH）加至数据包，以形成分组。网络表头包含了网络资料。  
例如:互联网协议（IP）等。

#### 2.数据链路层

数据链路层（Data Link Layer）负责网络寻址、错误侦测和改错。当表头和表尾被加至数据包时，会形成信息框（Data Frame）。  
数据链表头（DLH）是包含了物理地址和错误侦测及改错的方法。数据链表尾（DLT）是一串指示数据包末端的字符串。  
例如以太网、无线局域网（Wi-Fi）和通用分组无线服务（GPRS）等。  
分为两个子层：逻辑链路控制（logical link control，LLC）子层和介质访问控制（Media access control，MAC）子层。

#### 1.物理层

物理层（Physical Layer）在局域网上发送数据帧（Data Frame），它负责管理电脑通信设备和网络媒体之间的互通。  
包括了针脚、电压、线缆规范、集线器、中继器、网卡、主机接口卡等。  

## 通过协议传输应用层数据

下图简单的解释了，通过网络应用层的数据(hello)从A到B是如何传过去的。

[图片备用地址](https://limingxie.github.io/images/network/osi/OSI_1.png)  
![OSI](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/osi/OSI_1.png)

## 总结

OSI的7层协议是理论模型，我们实际落地时，经常会用到4层的TCP/IP协议族。  
为了方便研究和学习，也可以分为5层结构。  
后续将详细的整理每层的结构和作用。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`