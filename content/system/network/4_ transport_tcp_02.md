---
author: "li_mingxie"
title: "【网络协议笔记】第四层:传输层(Transport)TCP协议简介(2)"
date: 2022-04-16T06:05:49+08:00
tags: [
    "OSI",
    "tcp",
    "network",
]
categories: [
    "network",
]
---

TCP有以下几个知识点。这一篇整理【2.流量控制】和【3.拥塞控制】
<!--more-->
```
1.可靠传输(失败重传，保证不丢包)
2.流量控制(点对点，端对端；是通过控制接收窗口大小来控制的)
3.拥塞控制(一起维护网络环境，1.慢开始，2.用舌避免，3.快速重传，4快速回复)
4.建立连接(三次握手,四次挥手)
```

## 1.流量控制

如果接收方的缓存区满了，发送方还在疯狂发送数据，  
接收方只能把收到的数据包丢掉，大量的丢包会极大浪费网络资源，所以要进行流量控制。  

流量控制：让发送方的发送速率不要太快，让接收方来得及接收处理。
原理：通过确认报文中窗口字段来控制发送方的发送速率；  
发送方的发送窗口大小不能超过接收方给出的窗口大小；  
当发送方收到接收窗口的大小为0时，发送方就会停止发送数据。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_21.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_21.png?x-oss-process=image/resize,w_700,m_lfit)

有一种特殊情况，一开始，接收方给发送方发送了0窗口的报文段。  
后面，接收方又有了一些存储空间，给发送方发送的非0窗口的报文段丢失了。此时发送方的发送窗口一直为0，双方陷入僵局。  

解决方案就是：当发送方收到0窗口通知时，这时发送方停止发送报文。  
并且同时开启一个定时器，隔一段时间就发个测试报文去询问接收方最新的窗口大小，如果接收的窗口大小还是0，则发送方再次刷新启动定时器。  

MSS（Max Segment Size）：每个段最大的数据部分大小，是在建立连接时确定的，根据数据链路层倒推计算可得出MSS值是1460字节，  
但每次值大小是不确定的，需要双方通信时一起确定（取最小值）。  
计算方式：传输层数据1500字节 = 网络层(首部20字节 + 数据1480字节 = 传输层（首部20字节 + 数据1460字节（MSS））)  
cwnd（congestion window）：拥塞窗口（发送方）  
rwnd（receive window）：接收窗口（接收方）  
swnd（send window）：发送窗口，最大值取拥塞窗口和接收窗口的最小值（swnd = min(cwnd, rwnd)），  
当rwnd < cwnd时，是接收方的接收能力限制发送窗口的最大值，当rwnd > cwnd时，是网络的拥塞限制发送窗口的最大值。  

## 2.拥塞控制

拥塞控制：防止过多的数据注入到网络中，避免网络中的路由器或链路过载。

拥塞控制是一个全局性的过程，涉及到所有的主机、路由器以及与降低网络传输性能有关的所有因素，是大家共同努力的结果。相比而言，流量控制是点对点通信的控制。

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_22.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_22.png?x-oss-process=image/resize,w_700,m_lfit)

负载指的是经过路由器的带宽量总和。吞吐量就是带宽理论峰值。上图中链路吞吐量的理想情况是当负责超过带宽时，吞吐量达到峰值。  
但实际情况是，当负载越来越大（网络开始拥塞），吞吐量不会达到带宽值，并且之后就会下滑（网络拥塞开始严重），负载达到一定程度后网络就会卡死（死锁）。

可以类比一下交通。理论上一条8车道的道路可以同时开1000辆车，但实际700辆车就会发生拥堵。在早高峰或者晚高峰时，道路上的车辆开始出现缓慢移动，随着车辆越来越多，就会导致交通瘫痪。

### 2.1 控制方法

#### 2.1.1 慢开始/慢启动（slow start）

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_23.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_23.png?x-oss-process=image/resize,w_700,m_lfit)

接收方告诉发送方MSS=100, rwnd=3000，发送方根据MSS设置发送窗口(拥塞窗口)为cwnd=100，每一轮成功接收后，cwnd都呈指数级增长（2^n，最大不超过rwnd），这就是慢开始。

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_24.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_24.png?x-oss-process=image/resize,w_700,m_lfit)

当主机开始发送数据时，如果立即所大量数据字节注入到网络，那么就有可能引起网络拥塞，因为现在并不清楚网络的负荷情况。  
因此，较好的方法是先探测一下，即由小到大逐渐增大发送窗口，也就是说，由小到大逐渐增大拥塞窗口数值。  
通常在刚刚开始发送报文段时，先把拥塞窗口CWND设置为一个最大报文段MSS的数值。  
而在每收到一个对新的报文段的确认后，把拥塞窗口增加至多一个MSS的数值。  
用这样的方法逐步增大发送方的拥塞窗口CWND，可以使分组注入到网络的速率更加合理。  

为了防止拥塞窗口CWND增长过大引起网络拥塞，还需要设置一个慢开始门限ssthresh状态变量：  

当CWND < ssthresh时，使用上述的慢开始算法。  
当CWND > ssthresh时，停止使用慢开始算法而改用拥塞避免算法。  
当CWND = ssthresh时，既可使用慢开始算法，也可使用拥塞控制避免算法。  

#### 2.1.2 拥塞避免（congestion avoidance）

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_25.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_25.png?x-oss-process=image/resize,w_700,m_lfit)

ssthresh（slow start threshold）：慢开始阈值，cwnd达到阈值后，以线性方式增加。当网络出现频繁拥塞时，ssthresh值就下降的很快。  
拥塞避免（加法增大）：拥塞窗口缓慢增大，以防止网络过早出现拥塞。  
乘法减小：只要网络出现拥塞，把ssthresh减半，与此同时，执行慢开始算法（cwnd又恢复到初始值）。  
让拥塞窗口CWND缓慢地增大，即每经过一个往返时间RTT就把发送方的拥塞窗口CWND加1，而不是加倍。  
这样拥塞窗口CWND按线性规律缓慢增长，比慢开始算法的拥塞窗口增长速率缓慢得多。

当TCP连接进行初始化时，把拥塞窗口CWND置为1。  
为了便于理解，图中的窗口单位不使用字节而使用报文段（SMSS）的个数。慢开始门限的初始值设置为16个报文段，即CWND = 16。  
在执行慢开始算法时，拥塞窗口CWND的初始值为1。  
以后发送方每收到一个对新报文段的确认ACK，就把拥塞窗口值另1，然后开始下一轮的传输（图中横坐标为传输轮次）。  
因此拥塞窗口CWND随着传输轮次按指数规律增长。  
当拥塞窗口CWND增长到慢开始门限值ssthresh时（即当CWND=16时），就改为执行拥塞控制算法，拥塞窗口按线性规律增长。  
假定拥塞窗口的数值增长到24时，网络出现超时（这很可能就是网络发生拥塞了）。  
更新后的ssthresh值变为12（即变为出现超时时的拥塞窗口数值24的一半），拥塞窗口再重新设置为1，并执行慢开始算法。  
当CWND=ssthresh=12时改为执行拥塞避免算法，拥塞窗口按线性规律增长，每经过一个往返时间增加一个MSS的大小。  
强调： “拥塞避免”并非指完全能够避免了拥塞。利用以上的措施要完全避免网络拥塞还是不可能的。  
“拥塞避免”是说在拥塞避免阶段将拥塞窗口控制为按线性规律增长，使网络比较不容易出现拥塞。

怎么知道网络拥塞？通过丢包监测即可知道是否发生网络拥塞。  

#### 2.1.3 快速重传（fast retransmit）

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_26.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_26.png?x-oss-process=image/resize,w_700,m_lfit)

**接收方**： 每收到一个失序的分组后就立即发出重复确认，使发送发及时知道有分组没有到达，而不要等待自己发送数据时才进行确认。  
**发送方**： 只要连续收到三个重复确认（总共4个相同的确认），就应当立即重传对方尚未收到的报文段，而不必继续等待重传计时器到期后再重传。  

M3算法送的确认报段丢失后，发送端继续传递M4、M5和M6数据报，接收方确认数据报发送回去。  
快重传算法还规定，发送方只要一连收到三个重复确认就应当立即重传对方尚未收到的报文段M3，而不必继续等待M3设置的重传计时器到期。  
由于发送方尽早重传未被确认的报文段，因此采用快重传后可以使整个网络吞吐量提高约20%。

#### 2.1.4 快速恢复（fast recovery）

其过程有以下两个点：

当发送方连续收到三个重复确认，就执行“乘法减小”算法，把慢开始门限ssthresh减半。这是为了预防网络发生拥塞。请注意：接下去不执行慢开始算法。  
由于发送方现在认为网络很可能没有发生拥塞，因此与慢开始不同之处是现在不执行慢开始算法（即拥塞窗口CWND现在不设置为 1），  
而是把CWND值设置为慢开始门限ssthresh减半后的数值，然后开始执行拥塞避免算法（“加法增大”），使拥塞窗口缓慢地线性增大。  
下图给出了快重传和快恢复的示意图，并标明了“TCP Reno版本”。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_27.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_27.png?x-oss-process=image/resize,w_700,m_lfit)

也有的算法把开始时的重传的拥塞窗口值设置为ssthresh + 3 * MSS。  
这样做的理由是：既然发送方收到三个重复的确认，就表明有三个分组已经离开了网络。  
这三个分组不再消耗网络的资源而是停留在接收方的缓存中。  
可见现在网络中并不是堆积了分组而是减少了三个分组。因此可以适当把拥塞窗口扩大了些。  

在采用快恢复算法时，慢开始算法只是在 TCP 连接建立时和网络出现超时时才使用。  
采用这样的拥塞控制方法使得TCP的性能有明显的改进。  
当发送方连续收到三个重复确认，就执行“乘法减小”算法，把ssthresh减半，这是为了预防网络发生拥塞。  

由于发送方现在认为网络很可能没有发生拥塞，因此，与慢开始不同之处是现在不执行慢开始算法，  
即cwnd现在不恢复到初始值，而是把cwnd值设为ssthresh减半后的数值，然后开始执行拥塞避免算法（“加法增大”），使拥塞窗口缓慢的线性增大。  

### 2.2 总结

接收方根据自己的接收能力设定了接收窗口rwnd，并把这个窗口值写入TCP首部中的窗口字段，传送给发送方。  
因此，接收窗口又称为通知窗口。因此，从接收方对发送方的流量控制的角度考虑，发送方的发送窗口一定不能超过对方给出的接收窗口rwnd。  
发送方窗口的上限值swnd = Min(rwnd, cwnd)  
当rwnd < cwnd时，是接收方的接收能力限制发送方窗口的最大值。  
当cwnd < rwnd时，则是网络的拥塞限制发送方窗口的最大值。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
