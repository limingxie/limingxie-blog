---
author: "li_mingxie"
title: "【网络协议笔记】第二层:数据链路层(Data Link)简介"
date: 2022-04-12T19:05:49+08:00
tags: [
    "OSI",
    "data link",
]
categories: [
    "network",
]
---

数据链路层(Data Link)是网络协议中的第二层。  
**链路:** 从1个节点到相邻节点的一段物理线路(有线或无线)，中间没有其他交换节点。  
**数据链路:** 在一条链路上传输数据时，需要有对应的通信协议来控制数据的传输。  

不同类型的数据链路，所用的通信协议可能是不同的。  

**广播信道:** CSMA/CD协议（比如同轴电缆、集线器等组成的网络）  
**点对点信道:** PPP协议（比如2个路由器之间的信道）  
数据链路层的3个基本问题：  

1.封装成帧; 2.透明传输; 3.差错检验  

## 1.封装成帧

**帧(Frame)**的数据部分就是网络层传递下来的数据包（IP数据包，Packet）  
**最大传输单元**MTU（Maximum Transfer Unit）  
每一种数据链路层协议都规定了所能够传送的帧的数据长度上限  
以太网的MTU为1500个字节  

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_01.jpeg)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_01.jpeg?x-oss-process=image/resize,w_600,m_lfit)

## 2.透明传输

数据部分一旦出现了SOH、EOT，就需要进行转义。  

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_02.jpeg)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_02.jpeg?x-oss-process=image/resize,w_600,m_lfit)

由于在接收端在接收的时候把转义符还原了，感受不到数据的变化，所以是透明传输。  

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_03.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_03.png?x-oss-process=image/resize,w_900,m_lfit)

## 3.差错检验

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_04.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_04.png?x-oss-process=image/resize,w_600,m_lfit)

数据链路层首部是帧首部的一部分  
FCS是帧尾部的一部分，**FCS是根据数据部分+数据链路层首部计算**得出  
接收端接收到信息后会计算出FCS并进行比较，如果发现不一致，网卡就会把这条信息丢弃（抓包工具也抓不到）  
数据经过不同的数据链路层，对应的层会把之前的帧开始和结束符替换为自己的协议帧。  

## 4.CSMA/CD协议

CSMA/CD（Carrier Sense Multiple Access with Collision Detectio），  
载波侦听多路访问/冲突检测（主要是为了支持单工通信和半双工通信）。  
使用了CSMA/CD的网络可以称为是以太网（Ethernet），它传输的是以太网帧。  

以太网帧的格式有：Ethernet V2标准、IEEE的802.3标准  
现在使用最多的是：Ethernet V2标准  
为了能够检测正在发送的帧是否产生了冲突，**以太网的帧至少要64字节**。  

用交换机组件的网络，已经支持全双工通信，不需要再使用CSMA/CD，但它传输的帧依然是以太网帧。  
所以，用交换机组建的网络，依然可以叫做以太网。  

## 5.Ethernet V2帧的格式

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_05.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_05.png?x-oss-process=image/resize,w_900,m_lfit)

首部：目标MAC地址 + 源MAC地址 + 网络类型（IPV4/IPV6）  
以太网帧：首部 + 数据 + FCS  

以太网帧的数据长度：46 ~ 1500字节  
以太网帧的长度：64 ~ 1518字节（源MAC + 目标MAC + 网络类型 + 数据 + FCS）  

当数据部分（从网络层传入的数据）的长度小于46字节时(总长度不足64字节)，  
数据链路层会在数据的后面加入一些字节填充，接收端会将添加的字节去掉。  

## 6.PPP协议（Point to Point Protocol）

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_06.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_06.png?x-oss-process=image/resize,w_700,m_lfit)

Frame(F): PPP协议是有帧开始和结束符的，0x7E  
Address(A): 图中的值是0xFF，形同虚设，点到点信道不需要源MAC、目标MAC地址  
Control(C): 图中的值是0x03，目前没有什么作用  
Protocol(协议): 内部用到的协议类型（PPP协议的子分支协议）  
虽然PPP帧和以太网帧的协议不一样，但是网络层的数据是一样的，仅仅是帧的首部和尾部发生了变化。  

路由器和路由器直连时是PPP帧，如果在两个路由器之间加一个交换机，就不是PPP帧了，而是以太网帧。  
因为路由器之间是点对点，不需要知道对方的MAC地址，但是以太网帧是广播信道，每一台设备必须确认自己是否是接收方。  

PPP协议也是需要进行字节填充的：  
[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_08.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_08.png?x-oss-process=image/resize,w_600,m_lfit)

将0x7E替换成0x7D5E  
将0x7D替换成0x7D5D  

## 7.网卡

网卡接收到一个帧，首先会进行差错校验，如果校验通过则接收，否则丢弃。  
Wireshark抓到的帧是没有FCS，因为它抓到的是差错校验通过的帧，帧尾的FCS会被硬件去掉，所以抓不到差错校验失败的帧。  

[图片备用地址](https://limingxie.github.io/images/network/data_link/frame_07.png)  
![frame](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/data_link/frame_07.png?x-oss-process=image/resize,w_700,m_lfit)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
