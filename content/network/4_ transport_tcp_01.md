---
author: "li_mingxie"
title: "【网络协议笔记】第四层:传输层(Transport)TCP协议简介(1)"
date: 2022-04-24T21:05:49+08:00
tags: [
    "OSI",
    "tcp",
    "network",
]
categories: [
    "network",
]
---

TCP有以下几个知识点。

```
1.可靠传输(失败重传，保证不丢包)
2.流量控制(点对点，端对端；是通过控制接收窗口大小来控制的)
3.拥塞控制(一起维护网络环境，1.慢开始，2.用舌避免，3.快速重传，4快速回复)
4.建立连接(三次握手,四次挥手)
```

## 1.TCP（Transmission Control Protocol）：传输控制协议  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_01.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_01.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_02.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_02.png?x-oss-process=image/resize,w_700,m_lfit)

**TCP的几个要点：可靠传输、流量控制、拥塞控制、连接管理（建立和释放连接）。**  
也正因为这几点使得首部变得很复杂。

### 1.1 数据偏移（Reserved）

占4位，取值范围是0x0101 ~ 0x1111。  

乘以4就是首部长度（Header Length）。所以取值范围是5 ~ 60字节，由于首部固定部分占用20字节，所以可选部分至多占用40字节（和网络层首部一样）。  

为什么叫数据偏移？因为相对TCP报文向右偏移首部长度后就是数据部分。  
UDP的首部中有个16位的字段记录了整个UDP报文段的长度（首部 + 数据）。  
但是，TCP的首部中仅仅有个4位的字段记录了TCP报文段的首部长度，并没有字段记录TCP报文段的数据长度。  
分析：UDP首部中占16位的长度字段是冗余的，纯粹是为了保证首部是32bit对齐。  
TCP/UDP的数据长度，完全可以由IP数据包的首部推测出来，传输层的数据长度 = 网络层的总长度 - 网络层的首部长度 - 传输层的首部长度。  

### 1.2 保留（Reserved）

占6位，目前全为0。  

### 1.3 检验和（Checksum）

与UDP一样，TCP检验和的计算内容：伪首部 + 首部 + 数据。伪首部占用12字节，仅在计算检验和时起作用，并不会传递给网络层。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_03.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_03.png?x-oss-process=image/resize,w_700,m_lfit)

### 1.4 标志位（Flags）

一共占6位或9位。  

有些资料中，TCP首部的保留（Reserved）字段占3位，标志（Flags）字段占9位。Wireshark中也是如此。是因为标志位中的前3位是无用的，所以两种说法都不能说是错的。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_04.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_04.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_05.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_05.png?x-oss-process=image/resize,w_700,m_lfit)

#### 1.4.1 URG（Urgent）

意思：紧急。当URG=1时，紧急指针字段才有效。表明当前报文段中有紧急数据，应优先尽快传送。  
紧急指针存放的是长度值，表示TCP的前多少字节是需要紧急优先处理的。  

#### 1.4.2 ACK（Acknowledgment）

意思：确认。当ACK=1时，确认号字段才有效。  

#### 1.4.3 PSH（Push）

意思：推。一般用在交互式网络中。PUSH标志位所表达的是发送方通知接收方传输层应该尽快的将这个报文段交给应用层。  

#### 1.4.4 RST（Reset）

意思：重置。当RST=1时，表明连接中出现严重差错，必须释放连接，然后再重新建立连接。  

#### 1.4.5 SYN（Synchronization）

意思：同步。当SYN=1 & ACK=0时，表明这是一个建立连接的请求。若对方同意建立连接，则回复SYN=1 & ACK=1。  
请求方再发送SYN=0 & ACK=1时表明开始传输数据。这也是三次握手的流程。  

#### 1.4.6 FIN（Finish）

意思：完成。表明数据已经发送完毕，要求释放连接。  

### 1.5 序号（Sequence Number）

占4字节。首先，传输的每一个字节都会有一个编号（连续的字节编号也是连续的）。  
在建立连接后，序号代表这一次传给对方的TCP数据部分的第一个字节的编号。  

### 1.6 确认号（Acknowledgment Number）

占4字节。在建立连接后，确认号代表期望对方下一次传过来的TCP数据部分的第一个字节的编号。  

### 1.7 窗口（Window）

占2字节。这个字段有流量控制功能，用以告知对方下一次允许发送的数据大小（字节为单位）。  

## 2.可靠传输

### 2.1 停止等待ARQ协议

**ARQ(Automatic Repeat-reQuest), 自动重传请求。**

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_06.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_06.png?x-oss-process=image/resize,w_700,m_lfit)

无差错情况  

A发送数据M1到B，B收到数据M1后向A发送确认信号M1；  
A收到确认信号M1后，继续向B发送数据M2，B接收后向A发送确认信号M2。  
超时重传  

A发送数据M1到B，A在发送数据途中丢包或B发现数据M1有错误直接丢掉，导致B无法向A发送确认信号M1；  
A在一定时间间隔后发现没有收到B发送的确认信号M1，A会继续向B发送数据M1；  
B收到数据M1后向A发送确认信号M1，A收到确认信号M1后，继续向B发送M2数据。  
通过确认与超时重传机制实现可靠传输，在发送完一个分组后，必须暂时保留已发送的分组的副本。  
分组和确认分组都必须进行编号。超时计时器的重传时间应当比数据在分组传输的平均往返时间更长一些。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_07.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_07.png?x-oss-process=image/resize,w_700,m_lfit)

确认丢失  

A发送数据M1到B，B接收到数据M1后，向A发送确认信号M1；  
B在向A发送确认信号M1中途丢包，此时A在一定时间间隔后发现没有收到B发送的确认信号M1，A会继续向B发送数据M1；  
B收到数据M1后会丢弃重复的数据M1（之前已经收到数据M1，只是A不知道），继续向A发送确认信号M1；  
A收到确认信号M1后，继续开始发送M2数据。  
确认迟到  

A发送数据M1到B，B接收到数据M1后，向A发送确认信号M1；  
B在向A发送确认信号M1时，由于网络延迟等原因导致A在一定时间段内未收到确认信号；  
A会继续向B发送数据M1，B收到数据M1后丢弃重复的数据M1，并向A发送确认信号M1；  
A收到确认信号M1后，继续开始发送M2数据，M2数据刚发送出去，此时A刚好接收到B在第一次发送的确认信号M1，  
但由于之前已经成功接收并处理了第二次的确认信号M1，所以A在收到确认信号后什么也不做。  
出现差错或丢失的时候，发送方会将自己备份的副本再重传一次，直到收到接收的确认信息。  
当接收方收到重复的数据时，会直接丢弃，但是会给发送方请确认自己已经收到了。  

上面的停止等待协议每发送一组数据就必须等到接收方回复确认后，再发起第二组数据，如果出现超时重传的话，效率更低。  
因此为了提高传输的效率，改进了等待传输协议。  

### 2.2 连续ARQ协议和滑动窗口协议

连续ARQ协议和滑动窗口协议的机制是以接收方回复确认为单位，每次连续发送一个滑动窗口指定的数据组。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_08.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_08.png?x-oss-process=image/resize,w_900,m_lfit)

A发送数据给B时，一次性发送M1~M4（A和B建立连接时，B告诉A自己的缓存池可以容纳多少字节数据，  
A根据这个缓存池的大小构建一个同大小的发送窗口–也可以理解为发送缓存池），此时A开始等待确认，B收到全部数据后会向A发送确认信号M4（以最后一个编号为准）；  
A收到确认信号后，继续向B发送M5~M8（A把之前构建的窗口滑动并锁定到对应大小的数据段上，即M5~M8），以此往复直到数据传输完毕。  
如果接收窗口最多能接收4个包（窗口大小），但发送方只发了2个包，接收方如何确定后面还有没有2个包？  

答案：接收方会在等待一定时间后发现没有第3个包，就会返回收到2个包的确认信号给发送方。  

#### 2.2.1 什么是滑动窗口协议？(发送方怎么发送数据)

滑动窗口是由发送方维护的类似指针的变量，在每收到一个接收方的确认消息后，  
该指针向前移动并发送数据，到窗口指定大小的数据组时停下，等待接收方的确认。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_09.jpeg)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_09.jpeg)

#### 2.2.2 接收方怎么回复确认？

累积确认机制： 发送方不对收到的分组逐个发送确认，而是对按序到达的最后一个分组发送确认，  
这样就表示：到这个分组为止的所有分组都已正确收到了。  

优点：容易实现，即使确认丢失也不必重传。  
缺点：不能向发送方反映出接收方已经正确收到的所有分组的信息。  
Go-back-N（回退 N）： 为了解决上述同一窗口中数据组不能完整确认的问题，连续ARQ协议采用了回退机制。  
比如说：发送方发送了前5个分组，而中间的第3个分组丢失了。这时接收方只能对前两个分组发出确认。  
发送方无法知道后面三个分组的下落，而只好把后面的三个分组都再重传一次。这就叫做 Go-back-N（回退 N），表示需要再退回来重传已发送过的N个分组。  

结论：当通信线路质量不好时，连续ARQ协议会带来负面的影响。可能还不如传统的停止等待协议。  

### 2.3 TCP可靠传输的实现

TCP连接的每一端都必须设有两个窗口——一个发送窗口和一个接收窗口。  
TCP的可靠传输机制用字节的序号进行控制。TCP所有的确认都是基于序号而不是基于报文段。  
TCP两端的四个窗口经常处于动态变化之中。  
TCP连接的往返时间RTT也不是固定不变的。需要使用特定的算法估算较为合理的重传时间。  

#### 2.3.1 以字节为单位的滑动窗口技术

滑动窗口是面向字节流的，为了方便记住每个分组的序号，  
现在假设有一个1200字节的数据，分12组，每一组数据是100个字节，代表一个数据段的数据（每一个数据都有自己的TCP首部），每一组给一个编号（1~12）。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_10.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_10.png?x-oss-process=image/resize,w_900,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_11.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_11.png?x-oss-process=image/resize,w_900,m_lfit)

#### 2.3.2 选择确认(SACK)

TCP通信时，如果发送序列中间某个数据包丢失，TCP会通过重传最后确认的分组后续的分组，这样原先已经正确传输的分组也可能重复发送，降低了TCP性能。  
**SACK（Selective Acknowledgment，选择确认）技术**，使TCP只重新发送丢失的包，不用发送后续所有的分组，  
而且提供相应机制使接收方能告诉发送方哪些数据丢失，哪些数据已经提前收到等。  

在建立TCP连接时，就要在TCP首部的选项中加上“允许SACK”的选项，而双方必须都事先商定好。  
原来首部中的“确认号字段”的用法仍然不变。只是以后在TCP报文段的首部中都增加了SACK选项，以便报告收到的不连续的字节块的边界。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_12.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_12.png?x-oss-process=image/resize,w_700,m_lfit)

Kind：占1个字节，值为5代表这是SACK选项。  
Length：占1个字节，表明SACK选项一共占用多少字节。  
Left Edge：占4个字节，左边界。  
Right Edge：占4个字节，右边界。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_13.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_13.png?x-oss-process=image/resize,w_900,m_lfit)

上图的着色模块代表已接收数据，空白代表未接收数据。左右边界意思是会把未接收完毕的TCP数据包的已接收数据进行左右标记。  

由于TCP的选项不能超过40个字节，去除Kind和Length占用的2个字节，还剩下38个字节给左右边界使用。  
一组边界占用8个字节（左右边界各占4个字节），所以边界不能超过4组。也能够因此推断出SACK选项的最大占用字节数是4 * 8 + 2 = 34。  

思考：超过选项边界的数据怎么办？  
超过边界的数据需要重新传输，但这已经很大程度提高了传输效率。  

#### 2.3.3 超时重传时间的选择

重传机制是TCP中最重要和最复杂的问题之一。TCP每发送一个报文段，就对这个报文段设置一次计时器。  
只要计时器设置的重传时间到但还没有收到确认，就要重传这一报文段。那么这个重传时间到底应该设置多少呢？建议跳过，有兴趣的可以去查阅相关资料。  

[图片备用地址](https://limingxie.github.io/images/network/transport/tcp_14.png)  
![tcp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/transport/tcp_14.png?x-oss-process=image/resize,w_500,m_lfit)

为什么选择在传输层就将数据分割成多个段，而不是等到网络层再分片传递给数据链路层？  
-->网络层没有可靠传输协议，丢包无法只发送一个报文段，所以需要分割成多个段。

如果在传输层不分段，一旦出现数据丢失，整个传输层的数据都得重传  
如果在传输层分段了，一旦出现数据丢失，只需要重传丢失的那些段即可  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
