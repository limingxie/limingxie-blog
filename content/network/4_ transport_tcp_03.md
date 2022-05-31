---
author: "li_mingxie"
title: "【网络协议笔记】第四层:传输层(Transport)TCP协议简介(3)"
date: 2022-04-17T09:05:49+08:00
tags: [
    "OSI",
    "tcp",
    "network",
]
categories: [
    "network",
]
---

TCP有以下几个知识点。这一篇整理建立连接部分，包括三次握手和4次挥手。

```
1.可靠传输(失败重传，保证不丢包)
2.流量控制(点对点，端对端；是通过控制接收窗口大小来控制的)
3.拥塞控制(一起维护网络环境，1.慢开始，2.用舌避免，3.快速重传，4快速回复)
4.建立连接(三次握手,四次挥手)
```

## 1.TCP（Transmission Control Protocol）：传输控制协议  

这一篇我们看看TCP的【三次握手】和【四次挥手】。  
在看握手之前，需要对序号和确认号要有个清晰的概念才行。  

## 1.序号、确认号

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_31.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_31.png)

发送HTTP请求前先建立连接（三次握手）：  
发送方发送数据时，并不是每次发送TCP段都需要发送确认信号的，上图中每段都发送确认信号只是为了让我们更好的理解发送流程。  
序号是发送方为了让接收方知道当前发送的是哪一块数据。  
真正的序号并不是从1开始的（相对值），而是一个很大的值（原始值），只不过为了方便记忆及学习，wireshark已经计算处理。  
序号是在建立连接时已经确定的。  
ACK号是接收方告诉发送方下一次发送数据从哪一个字节开始。  

序号原始值为什么不能从1开始（简单值）？  
为了安全。当序号非常简单时，可以通过抓包得知下一个要发的包是什么，然后模拟包数据进行传输。  

### 序号和确认号交互流程

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_32.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_32.png?x-oss-process=image/resize,w_700,m_lfit)

建立连接时只有前两次的SYN是1（SYN=1），后面的SYN都是0（SYN=0）。连接建立后（三次握手）开始正式发送HTTP请求。  
在实际的传输过程中序号的初始值并不会是1，而是一个随机数防止被预测和被利用。看一下ACK号和序号传输过程：  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_33.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_33.png?x-oss-process=image/resize,w_700,m_lfit)

然而传送数据是双向的，服务端也会向客户端发送数据，客户端计算出初始序号然后与数据一起发给服务端，服务端收到后计算ACK号再返回去。  
而服务端也需要计算出一个自己的初始序号，与数据一起发送给客户端，然后客户端得出ACK返回给服务端。  

这样两边都告诉了对方自己的初始序号并开始传送数据，过程如图：  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_34.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_34.png?x-oss-process=image/resize,w_700,m_lfit)

客户端和服务器的序号初始值都是在TCP建立连接时确定的（值是随机的），并且会互相告知对方的序号。  
开始收发数据时，客户端的序号就是Seq = 上次客户端的序号（第一次是初始值） + TCP段字节序号，TCP段字节序号指的是TCP段的第几个字节，是一个相对值。  
服务器的ACK号就是ACK = 客户端的Seq + TCP段长度。服务端向客户端发送数据同理（用的是服务器的序号初始值）。  

比如客户端的序号初始值是123456，服务器的序号初始值是56789。  
连接建立后，客户端发送的第一个包的序号就是Seq = 123456 + 1 = 123457，假设TCP包长度Length是100个字节，服务端的ACK号就是：ACK = Seq + Length = 123557。  

发送方的序号和接收方的确认号都使用的是发送方的序号初始值。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_35.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_35.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_46.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_46.png?x-oss-process=image/resize,w_800,m_lfit)

把原始值s1和s2去掉，剩余的部分就是相对值。  

## 2.建立连接（三次握手）

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_36.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_36.png?x-oss-process=image/resize,w_700,m_lfit)

**CLOSED**：client处于关闭状态。  
**LISTEN**：server处于监听状态，等待client连接。  
**SYN-RCVD**：表示server接收到了SYN报文，当收到client的ACK报文后，它会进入到ESTABLISHED状态。  
**SYN-SEND**：表示client已发送SYN报文，等待server的第2次握手。  
**ESTABLISHED**：表示连接已经建立。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_37.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_37.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_38.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_38.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_39.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_39.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_40.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_40.png?x-oss-process=image/resize,w_700,m_lfit)

前2次握手的特点：  

* SYN都设置为1  
* 数据部分的长度都为0  
* TCP头部的长度一般是32字节，固定部分是20字节，选项部分是12字节  

双方会交换确认一些信息，比如MSS、是否支持SACK、Window scale（窗口缩放系数）等。这些数据都放在TCP头部的选项部分中（12字节）。  

看看3次握手的抓包数据：  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_45.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_45.png)

为什么建立连接的时候，要进行3次握手？2次不行么？  
主要目的是防止server端一直在等待，浪费资源。  

```
如果建立连接只需要2次握手。假设client发出的第一个连接请求报文段，因为网络延迟，在连接释放以后的某个时间才到达server。  
本来这是一个早已失效的连接请求，但server收到此失效的请求后，误认为是client再次发出的一个新的连接请求，于是server就向client发出确认报文段，同意建立连接；  
如果不采用3次握手，那么只要server发出确认，新的连接就建立了；  
由于现在client并没有真正想连接服务器的意愿，因此不会理睬server的确认，也不会向server发送数据；  
但server却以为新的连接已经建立，并一直等待client发来的数据，这样server的很多资源就白白浪费掉了。  
采用三次握手的办法就可以防止上述现象的发生。例如上述情况，client没有向server的确认发出确认，server由于收不到确认，就知道client并没有要求建立连接。  
```

第三次握手失败了，会怎么处理？  

```
此时server的状态为SYN-RCVD，若等不到client的ACK，server会重新发送SYN+ACK包；  
如果server多次重发SYN+ACK都等不到client的ACK，就会发送RST包，强制关闭连接。  
```

## 3.释放连接（四次挥手）

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_41.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_41.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_42.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_42.png?x-oss-process=image/resize,w_700,m_lfit)

**FIN-WAIT-1**：表示想主动关闭连接。  
向对方发送了FIN报文，此时进入到 FIN-WAIT-1状态。

**CLOSE-WAIT**：表示在等待关闭。  
当对方发送FIN给自己，自己会回应一个ACK报文给对方，此时进入到CLOSE-WAIT状态。  
在此状态下，需要考虑自己是否还有数据要发送给对方，如果没有，发送FIN报文给对方。  

**FIN-WAIT-2**：只要对方发送ACK确认后，主动方就会处于FIN-WAIT-2状态，然后等待对方发送FIN报文。  

**CLOSING**：一种比较罕见的例外状态。  
表示你发送FIN报文后，并没有收到对方的ACK报文，反而却也收到了对方的FIN报文。  
如果双方几乎在同时准备关闭连接的话，那么就出现了双方同时发送FIN报文的情况，也即会出现CLOSING状态。  
表示双方都正在关闭连接。  

**LAST-ACK**：被动关闭一方在发送FIN报文后，最后等待对方的ACK报文。当收到ACK报文后，即可进入CLOSED状态。  

**TIME-WAIT**：表示收到了对方的FIN报文，并发送出了ACK报文，就等2MSL后即可进入CLOSED状态。  
如果FIN-WAIT-1状态下收到了对方同时带FIN标志和ACK标志的报文时，可以直接进入到TIME-WAIT状态，而无须经过FIN-WAIT-2状态。  

**CLOSED**：关闭状态。  
由于有些状态的时间比较短暂，所以很难用netstat命令看到，比如SYN-RCVD、FIN-WAIT等。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_43.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_43.png?x-oss-process=image/resize,w_700,m_lfit)

为什么释放连接要经过TIME-WAIT状态？能直接释放么？  
TCP/IP协议栈在设计上，允许任何一方先发起断开请求。这里演示的是client主动要求断开的。  

client发送ACK后，需要有个TIME-WAIT阶段，等待一段时间后，再真正关闭连接。一般是等待2倍的MSL（Maximum Segment Lifetime，最大分割生存期）。  
MSL是TCP报文在Internet上的最长生存时间，每个具体的TCP实现都必须选择一个确定的MSL值，RFC1122建议是2分钟。  
可以防止本次连接中产生的数据包误传到下一次连接中（因为本次连接中的数据包都会在2MSL时间内消失）。  

如果client发送ACK后马上释放了，然后又因为网络原因server没有收到client的ACK，server就会重发FIN。这时可能出现的情况是：  

* client没有任何响应，服务器那边会干等，甚至多次重发FIN，浪费资源。  
* client有个新的应用程序刚好分配了同一个端口号，新的应用程序收到FIN后马上开始执行断开连接的操作，本来它可能是想跟server建立连接的。  

client在TIME-WAIT状态下，主要是等待server是否会继续发送FIN（防止client的ACK丢包）。  

#### 为什么释放连接的时候，要进行4次挥手？

主要原因是：TCP是双工模式。  

**第1次挥手**：当主机1发出FIN报文段时  
表示主机1告诉主机2，主机1已经没有数据要发送了，但是此时主机1还是可以接收来自主机2的数据  
**第2次挥手**：当主机2返回ACK报文段时  
表示主机2已经知道主机1没有数据发送了，但是主机2还是可以发送数据到主机1的  
**第3次挥手**：当主机2也发送了FIN报文段时  
表示主机2告诉主机1，主机2已经没有数据要发送了  
**第4次挥手**：当主机1返回ACK报文段时  
表示主机1已经知道主机2没有数据发送了，随后正式断开整个TCP连接  

#### 有时候会有3次挥手

这是把第2次挥手和，第3次挥手给合并了。  
当被动方接受发现自己也没有数据要发送时，会发送的时候合并第2次挥手和第3次挥手。  
(一般关闭游览器时，很有可能发生3次挥手了。）

看看下面你的抓包数据:  
[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_44.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_44.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
