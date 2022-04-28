---
author: "li_mingxie"
title: "【网络协议笔记】计算机之间的通信和链接方式"
date: 2022-04-16T22:28:49+08:00
tags: [
    "ARP",
    "Hub",
    "Bridge",
    "Switch",
    "Router",
]
categories: [
    "network",
]
---

## 1.计算机之间的通信

计算机之间的通信是通过网络进行的，网络是计算机之间的一种通信方式。  
那我们要如何进行网络通信呢？  
首先我们需要知道对方的IP地址，通过IP地址获取MAC地址(ARP广播)。  
然后把数据通过网卡传送到对方的网络中。  

如果网卡发现数据的目标MAC地址是自己，就会将数据传递给上一层进行处理。  
如果网卡发现数据的目标MAC地址不是是自己，就会将数据丢掉。

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/computer_communication.png)
![computer_communication](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/computer_communication.png)

## 2.计算机之间的链接方式

下面我们看一下具体的链接方式。

#### 2.1. 网线直连  

两台计算机之间最简单的连接方式就是网线直连（交叉线）。

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/zhilian.png)
![zhilian](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/zhilian.png)

```
通信的时候，会使用到ICMP、ARP协议。  
ICMP是数据包传送的时候用到的协议，ARP是获取MAC地址的协议。  

ARP协议做的是给同一个网段的所有电脑发广播（ARP是有缓存的）。  
如果自己是广播里寻找的IP，当前计算机把自己的网卡地址（广播找MAC地址）已广播的形式再发出去。
```

#### 2.2. 同轴电缆

如果需要链接多台电脑怎么办？

第一个出现的同轴电缆的方式，是半双工通信方式。  
容易冲突（冲突域），而且只要一个连接点出问题全域瘫痪。  
一台设备在发送数据的时候，另一台设备只能接收数据，不能发送数据。  

#### 2.3. 集线器（Hub）  

采用的也是半双工通信，但是比同轴电缆有个特甜：如果一台设备的链接出现问题也不会影响其他设备。  

集线器和同轴电缆都是没有智商的，是个机械性的设备。  
只要有设备发数据，就把数据转给其它所有已连接设备，设备越多效率越低。

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/hub.png)
![hub](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/hub.png?x-oss-process=image/resize,w_600,m_lfit)

下面是集线器传输数据的动态图

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/hub_gif.gif)
![hub](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/hub_git.gif?x-oss-process=image/resize,w_600,m_lfit)

#### 2.4. 网桥（Bridge）

网桥连接的是同一个网段的计算机。  
它可以记录左右两边的MAC地址，减少没必要的广播和传输，从而起到隔绝冲突域的作用。  

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/bridge.png)
![bridge](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/bridge.png?x-oss-process=image/resize,w_600,m_lfit)

#### 2.5. 交换机（Switch）

交换器就比网桥更牛逼了，他可以记录每一个网络节点的IP和MAC地址。  
还有交换机是全双工通信，没有冲突域。
而且不会发数据发到其他设备，所以比集线器安全（不会因为抓包而拦截数据）。

但是首次发送数据时，还是会ARP广播所有设备，因为此时交换机还不知道源和目标的MAC地址。

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/switch.png)  
![switch](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/switch.png?x-oss-process=image/resize,w_600,m_lfit)

#### 2.6. 路由器（Router）

以上的设备必须是在同一个网段和广播域，但是不能把所有的电脑用交换链接起来。  
好几亿台的电脑用过广播寻找对应IP地址的Mac地址，那这网络遭遇“广播风暴”了。

所以路由的作用就出来了，路由是隔绝广播域，如果是在不同网段之间转发数据，就直接去寻找路由。  
那如何去寻找路由呢？ 是通过网关。我们在设置IP地址时，甚至的默认网关地址就是路由的地址。  
(一般IP地址的最后设置成1。比如 192.168.0.1)。  

主机发数据前，首先会判断目标主机的IP地址跟它是否在同一个网段。  
如果在同一个网段：ARP广播，直接发送数据；  
如果不在同一个网段：通过路由器转发数据。  

[图片备用地址](https://limingxie.github.io/images/network/computer_communication/router.png)  
![switch](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/computer_communication/router.png?x-oss-process=image/resize,w_900,m_lfit)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
