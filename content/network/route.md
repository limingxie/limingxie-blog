---
author: "li_mingxie"
title: "【网络协议笔记】路由工作原理简介"
date: 2022-04-10T17:20:49+08:00
tags: [
    "route",
]
categories: [
    "network",
]
---

我们知道不同网段的设备转发数据，需要路由器的支持。  
默认情况下，路由器是只知道跟他直连的网段，非直连的网段是需要通过静态路由或动态路由告诉它。  

1. 静态路由  
静态路由是管理员手动去添加路由设备信息，使用与小规模的网络。
2. 动态路由  
路由器通过路由选择协议(RIP,OSPF)自动获取路由信息，适用于大规模的网络。

我们先看下图路由是如何连接设备的：

```
1.计算机是和路由的网关连接，路由网关和计算机必须是在同一个网段。
2.路由和路由之间是连接也是必须在同一个网段。
3.如果路由连接多个路由的话，通过路由表的下一跳决定发送给哪个路由。
```

[图片备用地址](https://limingxie.github.io/images/network/route/route.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route.png?x-oss-process=image/resize,w_800,m_lfit)

----------------------------------------------

**下图是给路由配置网关**  

[图片备用地址](https://limingxie.github.io/images/network/route/route_01.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_01.png?x-oss-process=image/resize,w_800,m_lfit)

----------------------------------------------

**给路由的路由表设置网段下一跳的路由地址**

[图片备用地址](https://limingxie.github.io/images/network/route/route_02.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_02.png?x-oss-process=image/resize,w_800,m_lfit)

----------------------------------------------

**下面是路由表里保存的信息**  

* C代表直连（connected）  
* S代表静态（static）  

[图片备用地址](https://limingxie.github.io/images/network/route/route_03.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_03.png?x-oss-process=image/resize,w_800,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/route/route_04.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_04.png?x-oss-process=image/resize,w_800,m_lfit)

----------------------------------------------

**下面简单的整理了**  
**下一跳的路由表是如何设定的**  

[图片备用地址](https://limingxie.github.io/images/network/route/route_06.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_06.png?x-oss-process=image/resize,w_800,m_lfit)

----------------------------------------------

**数据经过路由的时候MAC地址是有变化的。**  
**如下图：**  

[图片备用地址](https://limingxie.github.io/images/network/route/route_05.png)  
![route](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/route/route_05.png?x-oss-process=image/resize,w_900,m_lfit)

#### 以上是路由传送数据时的简单原理

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
