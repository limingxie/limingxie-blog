---
author: "li_mingxie"
title: "【网络协议笔记】第三层:网络层(Network)简介"
date: 2022-04-22T19:05:49+08:00
tags: [
    "OSI",
    "network",
]
categories: [
    "network",
]
---

网络层数据包（IP数据包：Packet）由首部、数据2部分组成。  
**数据:** 由传输层传递下来的数据段(Segment)。  

下图是网络层的数据包结构：  
[图片备用地址](https://limingxie.github.io/images/network/network/network_01.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_01.png?x-oss-process=image/resize,w_800,m_lfit)

**下图是使用Wireshark抓包的信息:**  
[图片备用地址](https://limingxie.github.io/images/network/network/network_02.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_02.png?x-oss-process=image/resize,w_800,m_lfit)

#### 1.版本（Version）

占4位（0b0100：IPv4，0b0110：IPv6）。  

#### 2.首部长度（Header Length）

占4位，标识该IP头部有多少个32bit字（4字节），因为4位最大能表示15，所以IP头部最长是60字节。  

最小值：0b0101(5 x 4 = 20)  
最大值：0b1111(15 x 4 = 60)  

首部固定部分是20个字节，虽然还有可变部分，但很多时候就是20个字节。  
由于最大值是60个字节，所以可变部分是40个字节。  

#### 3.区分服务（Differentiated Services Field）

占8位，可以用于提高网络的服务质量（QoS：Quality of Service）  
包括3位优先级字段、1位保留字段和4位TOS字段。4位TOS分别表示：最小时延、最大吞吐量、最高可靠性和最小费用。其中最多有一个能置为1。  
比如客户端发送数据给服务端，数据在经过路由器的时候，路由器识别区分服务（例如，区分服务的优先级值为3）的优先级后可以让这部分数据优先通过。  

#### 4.总长度（Total Length）

占16位。指整个IP数据报的长度，以字节为单位。首部 + 数据的长度之和，最大值是65535（2^16-1）字节。  

[图片备用地址](https://limingxie.github.io/images/network/network/network_03.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_03.png?x-oss-process=image/resize,w_700,m_lfit)

但是由于MTU限制(帧的数据不能超过1500字节)，长度超过MTU的数据报将会分片传输，所以实际传输的IP数据报长度远远没有达到最大值。  
因此过大的IP数据包，需要分成片(fragment)，每一片都有自己的网络层首部(IP首部)。  

标识、标志、片偏移描述了如何分片。  

#### 5.标识（Identification）

占16位，数据包的ID。其初始值是系统随机生成，每发送一个数据包，其值加1。  
该值在分片时被复制到每一个分片中，因此同一个数据包的所有分片的标识都是一样的。  

#### 6.标志（Flags）

占3位。  

第1位（Reserved Bit）：保留  
第2位（Don’t Fragment）：表示“是否禁止分片”，1代表禁止分片，0代表允许分片  
第3位（More Fragments）：表示“是否还有更多片”，1代表不是最后一片，0代表是最后一片。如果要分片，除了最后一个分片外，其他的分片都要置1。  

[图片备用地址](https://limingxie.github.io/images/network/network/network_04.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_04.png?x-oss-process=image/resize,w_800,m_lfit)

#### 7.片偏移（Fragment Offset）

占13位。是分片相对原始IP数据报开始处的偏移（仅指数据部分）。  
实际偏移值是该值左移3位得到的。所以除最后一个分片，其他分片的数据部分长度必须是8的整数倍（片偏移乘以8：字节偏移）。  

[图片备用地址](https://limingxie.github.io/images/network/network/network_05.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_05.png?x-oss-process=image/resize,w_800,m_lfit)

#### 8.生存时间（Time to Live）

占8位。设置了数据可以经过的最多的路由器数（操作系统不同TTL值也不同），  
每个路由器在转发之前会将TTL减1，如果该值减为0依旧没有到达目的主机，  
就丢弃该数据包，由这个路由器发送ICMP差错报文（目标不可达）。  
主要作用就是防止路由死循环（A路由跳B路由，B路由跳A路由，数据一直在往返，如果没有TTL就会产生死循环）。  

[图片备用地址](https://limingxie.github.io/images/network/network/network_06.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_06.png?x-oss-process=image/resize,w_700,m_lfit)

观察使用ping命令后的TTL，能够推测出对方的操作系统，中间经过了多少个路由器。  

#### 9. 协议（Protocol）

用于区分上层协议，表明所封装的数据使用了什么协议。  

[图片备用地址](https://limingxie.github.io/images/network/network/network_07.png)  
![network](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/network/network_07.png?x-oss-process=image/resize,w_700,m_lfit)

#### 10.首部检验和（Header Checksum）

由发送端填充，检查首部是否有错误。接收端对其使用CRC算法检验IP数据报首部在传输过程中是否损坏（只检查首部，不管数据部分）  

#### 11.源IP地址和目标IP地址（Source Address、Destination Address）

各占4个字节。用来指定发送端和接收端的。  

#### 12.ping的几个相关用法

查看ping的用法: ping -h
指定接收包的次数：ping -c 3 www.baidu.com  
发送指定大小的数据包: ping -s 100 www.baidu.com  
设置TTL的值: ping 182.61.200.7 -m 2  
通过traceroute命令，可以跟踪数据包经过了哪些路由器  
Mac系统首次使用ping设置网络层，可能会提示权限不足，需要授权sudo。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
