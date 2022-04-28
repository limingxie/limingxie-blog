---
author: "li_mingxie"
title: "【网络协议笔记】第四层:传输层(Transport)以及UPD协议简介"
date: 2022-04-23T21:05:49+08:00
tags: [
    "OSI",
    "network",
]
categories: [
    "network",
]
---

## 1.传输层（Transport）

TCP和UDP的区别：
[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_udp.png)  
![tcp_udp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_udp.png?x-oss-process=image/resize,w_700,m_lfit)

#### 1.1 连接性

TCP是面向连接，也就是说TCP传输必须先要建立连接才能通信，建立连接就是我们熟知的“三次握手”。  
TCP在数据传输完毕后需要断开连接，因为服务器会有端口不断的监听本次连接的状态，如果不断开就会一直占用资源，断开连接需要“四次挥手”。  

UDP是无连接，通信时不需要建立连接，发送方直接发送数据，不关心接收方的接收状态。  

#### 1.2 可靠性

TCP：可靠传输，不丢包。只要发现丢包就会重新发送，会保证数据是准确的。  
UDP：不可靠传输，可能丢包。只管发送，不管接收方的是否接收到。  

#### 1.3 首部占用空间

TCP：由于是建立连接的可靠传输，所以首部会比较复杂，占用空间也就会比较大。  
UDP：几乎是无脑传输，所以只需要知道源和目标信息就可以了。相比TCP就会小很多。  

#### 1.4 传输速率

UDP的特点使得其传输速率比TCP快。  

#### 1.5 资源消耗

TCP由于需要建立连接，所以会比较消耗资源。  
UDP资源消耗就比较小。  

#### 1.6 应用场景

浏览器、文件传输、邮件发送等都需要保证数据的完整性，所以使用TCP比较合适。  
音视频通话、直播等对时效性要求较高，所以使用UDP更佳。  
如果使用TCP就会发生数据混乱，比如3s前的音/影由于丢包原因在2min后才成功发送到接收方。  

#### 1.7 应用层协议

浏览器：HTTP、HTTPS
文件传输：FTP
邮件：SMTP
DNS

## 2.UDP

UDP（User Datagram Protocol）：用户数据报协议。  
UDP是无连接的，减少了建立和释放连接的开销。  
UDP尽最大能力交付，不保证可靠交付，因此不需要维护一些复杂的参数，首部只有8个字节（TCP的首部至少20个字节）。  

[图片备用地址](https://limingxie.github.io/images/network/transport/udp_01.png)  
![udp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/udp_01.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/udp_02.png)  
![udp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/udp_02.png?x-oss-process=image/resize,w_700,m_lfit)

#### 2.1 UDP长度（Length）

占16位，首部的长度 + 数据的长度。  

#### 2.2 检验和（Checksum）

检验和的计算内容：伪首部 + 首部 + 数据。  

[图片备用地址](https://limingxie.github.io/images/network/transport/udp_06.png)  
![udp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/udp_06.png?x-oss-process=image/resize,w_700,m_lfit)

伪首部是固定的12字节，  
由源IP地址（4个字节） + 目的IP地址（4个字节） + 保留位（1个字节，默认是0） + 协议代表值（1个字节，UDP固定值17） + UDP长度（2个字节）组成。  
仅在计算检验和时起作用，并不会传递给网络层，目的是为了增强检验功能。  

#### 2.3 端口（Port）

UDP首部中端口占用2字节。可以推测出端口号的取值范围是：0 ~ 65535。  
客户端的源端口是临时开启的随机端口。  
防火墙可以设置开启/关闭某些端口来提供安全性。  

[图片备用地址](https://limingxie.github.io/images/network/transport/udp_03.png)  
![udp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/udp_03.png?x-oss-process=image/resize,w_400,m_lfit)  

**常用命令行：**  

**查看被占用的端口: lsof -i 80 或 lsof -i tcp:80**  
**telnet 主机 端口：查看是否可以访问主机的某个端口。**  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
