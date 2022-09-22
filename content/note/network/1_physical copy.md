---
author: "li_mingxie"
title: "【网络协议笔记】第一层:物理层(Physical)简介"
date: 2022-04-11T19:05:49+08:00
tags: [
    "OSI",
    "physical",
]
categories: [
    "network",
]
---

物理层定义了接口标准、线缆标准、传输速率、传输方式等（比如路由器定义的接口是做什么的、水晶头怎么排布、光缆线的作用等）。<!--more-->

## 1.信号类型

[图片备用地址](https://limingxie.github.io/images/network/physical/physical_01.png)  
![physical](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/physical/physical_01.png)

#### 1.1 模拟信号（Analog Signal）

连续的信号，适合长距离传输  
抗干扰能力差，受到干扰时波形变形很难纠正  

#### 1.2 数字信号（Digital Signal）

离散的信号，不适合长距离传输  
抗干扰能力强，受到干扰时波形失真可以修复  

## 2.数据类型通信模型

#### 2.1 局域网通讯模型

[图片备用地址](https://limingxie.github.io/images/network/physical/physical_02.png)  
![physical](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/physical/physical_02.png?x-oss-process=image/resize,w_900,m_lfit)

#### 2.2 广域网通讯模型

[图片备用地址](https://limingxie.github.io/images/network/physical/physical_03.png)  
![physical](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/physical/physical_03.png?x-oss-process=image/resize,w_900,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/physical/physical_04.png)  
![physical](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/physical/physical_04.png?x-oss-process=image/resize,w_900,m_lfit)

## 3.信道

信道：信息传输的通道，一条传输介质上（比如网线）上可以有多条信道。

#### 3.1 单工通信

信号只能往一个方向传输，任何时候都不能改变信号的传输方向  
比如无线电广播、有线电视广播  

#### 3.2 半双工通信

信号可以双向传输，但必须是交替进行，同一时间只能往一个方向传输  
比如对讲机  

#### 3.3 全双工通信

信号可以同时双向传输  
比如手机（打电话，听说同时进行）  

#### 简单的说物理层是定义线缆，接口，通讯等相关的标准

(其实之外的也不太懂...^^;;)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
