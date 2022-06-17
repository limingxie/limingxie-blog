---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTPS协议简介(9)"
date: 2022-04-26T18:05:49+08:00
tags: [
    "OSI",
    "HTTP2",
    "HTTP3",
    "SPDY",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了HTTP2和HTTP3。  
<!--more-->
## 1.HTTP协议的不足（HTTP/1.1）

1. 同一时间，一个连接只能对应一个请求（注意：不是建立多个连接，是多个请求只能在一个连接内队列等待）。  
针对同一个域名，大多数浏览器允许同时最多6个并发连接。  

2. 只允许客户端主动发起请求（请求应答模式，即一个请求只能对应一个响应）。  

3. 同一个会话的多次请求中，头信息会被重复传输。  
通常会给每个传输增加500~800字节的开销。如果使用Cookie，增加的开销有时会达到上千字节。  

### 1.1 SPDY

SPDY（speedy的缩写），是基于TCP的应用层协议，它强制要求使用SSL/TLS。  
2009年11月，Google宣布将SPDY作为提高网络速度的内部项目。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_101.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_101.png?x-oss-process=image/resize,w_700,m_lfit)  

### 1.2 SPDY与HTTP的关系

* SPDY并不用于取代HTTP，它只是修改了HTTP请求与响应的传输方式  
* 只需增加一个SPDY层，现有的所有服务端应用均不用做任何修改  
* SPDY是HTTP/2的前身  

2015年9月，Google宣布移除对SPDY的支持，拥抱HTTP/2。  

## 2.HTTP/2

HTTP/2，于2015年5月以RFC_7540正式发表。根据W3Techs的数据，截止2019年6月，全球有36.5%的网站支持了HTTP/2。  

HTTP/1.1和HTTP/2速度对比：  
<http://www.http2demo.io>  
<https://http2.akamai.com/demo>  

[图片备用地址](https://limingxie.github.io/images/network/application/application_102.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_102.png?x-oss-process=image/resize,w_700,m_lfit)  

HTTP/2在底层传输做了很多的改进和优化，但在语意上完全与HTTP/1.1兼容。  
比如请求方法（如GET、POST）、Status Code，各种Headers等都没有改变，  
因此要想升级到HTTP/2，开发者不需要修改任何代码，只需要升级服务器配置，升级浏览器。  

**注意：SPDY强制使用TLS，但是HTTP/2文档并没有说明强制使用TLS，但在开发中还是建议使用TLS。**  

### 2.1 基本概念

**数据流:** 已建立的连接内的双向字节流，可以承载一条或多条消息。  
所有通信都在一个TCP连接上完成，同一时间内此连接可以承载任意数量的双向数据流。  

**消息:** 与逻辑HTTP请求或响应消息对应，由一系列帧组成。

**帧:** HTTP/2通信的最小单位，每个帧都包含帧头（会标识出当前帧所属的数据流），  
来自不同数据流的帧可以交错发送，然后再根据每个帧头的数据流标识符重新组装。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_103.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_103.png?x-oss-process=image/resize,w_700,m_lfit)  

### 2.2 HTTP/2的特性

#### 2.2.1 二进制格式

HTTP/2采用二进制格式传输数据，而非HTTP/1.1的文本格式。  

二进制格式在协议的解析和优化扩展上带来更多的优势和可能。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_104.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_104.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 2.2.2 多路复用（Multiplexing）

* 客户端和服务器可以将HTTP消息分解为互不依赖的帧，然后交错发送，最后再在另一端把它们重新组装起来。  

* 并行交错地发送多个请求，请求之间互不影响。  

* 并行交错地发送多个响应，响应之间互不干扰。  

* 使用一个连接并行发送多个请求和响应。  

* 不必再为绕过HTTP/1.1限制而做很多工作，  
比如image sprites（精灵图）、合并CSS/JS（一起放入一个文件中）、内嵌CSS/JS/Base64图片、域名分片（n个域名就能有6n个连接）等。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_105.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_105.png?x-oss-process=image/resize,w_700,m_lfit)  

**精灵图是前端开发中的概念，image sprites（也叫作CSS Sprites），将多张小图合并成一张大图，**  
**最后通过CSS（属性background:）结合小图的位置，尺寸进行精准定位。**

[图片备用地址](https://limingxie.github.io/images/network/application/application_106.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_106.png?x-oss-process=image/resize,w_700,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_107.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_107.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 2.2.3 优先级

* HTTP/2标准允许每个数据流都有一个关联的权重和依赖关系  
    可以向每个数据流分配一个介于1至256之间的整数  
    每个数据流与其他数据流之间可以存在显式依赖关系  

* 客户端可以构建和传递“优先级树”，表明它倾向于如何接收响应  

* 服务器可以使用此信息通过控制CPU、内存和其他资源的分配设定数据流处理的优先级  
    在资源数据可用之后，确保将高优先级响应以最优方式传输至客户端  

[图片备用地址](https://limingxie.github.io/images/network/application/application_108.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_108.png?x-oss-process=image/resize,w_700,m_lfit)  

应尽可能先给父数据流分配资源。同级数据流（共享相同父项）应按其权重比例分配资源。  

1. A、B依赖于隐式“根数据流”，A获得的资源比例是12/16，B获得的资源比例是4/16。  
2. D依赖于根数据流，C依赖于D，D应先于C获得完整资源分配。  
3. D应先于C获得完整资源分配，C应先于A和B获得完整资源分配，B获得的资源是A所获资源的1/3。  
4. D应先于E和C获得完整资源分配，E和C应先于A和B获得相同的资源分配，B获得的资源是A所获资源的1/3。  

#### 2.2.4 头部压缩

[图片备用地址](https://limingxie.github.io/images/network/application/application_109.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_109.png?x-oss-process=image/resize,w_700,m_lfit)  

HTTP/2使用HPACK压缩请求头和响应头，可以极大减少头部开销，进而提高性能。  

早期版本的HTTP/2和SPDY使用zlib压缩，可以将所传输头数据的大小减少85%~88%。  
但在2012年夏天，被攻击导致会话劫持，后被更安全的HPACK取代。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_110.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_110.png?x-oss-process=image/resize,w_700,m_lfit)  

客户端和服务端都会缓存一张请求头静态表（Static table），当客户端发送请求时，会检测客户端侧的请求头表中是否有同样的头部数据，  
如果有，只需要向服务器发送头部对应的表中数据索引即可，这样就能减少头部的数据，达到头部压缩目标。  

#### 2.2.5 服务器推送（Server Push）

服务器可以对一个客户端请求发送多个响应。  
除了对最初请求的响应外，服务器还可以向客户端推送额外资源，而无需客户端额外明确地请求。

[图片备用地址](https://limingxie.github.io/images/network/application/application_111.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_111.png?x-oss-process=image/resize,w_700,m_lfit)  

### 2.3 HTTP/2的问题

#### 2.3.1 队头阻塞（head of line blocking）

TCP在传输数据的时候是有顺序的，而HTTP/2请求或响应是无序，如果TCP传输过程中，队列头部请求丢包，后面的请求都将无效，只能全部重新请求或响应。  
这是TCP的问题，不是HTTP的问题。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_112.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_112.png?x-oss-process=image/resize,w_700,m_lfit)  

QUIC协议就解决了上面队头阻塞问题，如果发现丢包，不影响其他数据的组装。主要原因是QUIC底层是用UDP实现的。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_113.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_113.png?x-oss-process=image/resize,w_700,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_114.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_114.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 2.3.2 握手延迟

[图片备用地址](https://limingxie.github.io/images/network/application/application_115.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_115.png?x-oss-process=image/resize,w_700,m_lfit)  

**RTT（Round Trip Time）**：往返时延，可以简单理解为通信一来一回的时间。  

由于TCP本身需要3次握手，使用TLS后会延迟握手时间。QUIC因为使用的是UDP，所以不存在握手环节。  

## 3.HTTP/3

Google觉得HTTP/2仍然不够快，于是就有了HTTP/3。  

HTTP/3由Google开发，弃用TCP协议，改为使用基于UDP协议的QUIC协议实现。  

QUIC（Quick UDP Internet Connections），快速UDP网络连接。  
由Google开发，在2013年实现，于2018年从HTTP-over-QUIC改为HTTP/3。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_116.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_116.png?x-oss-process=image/resize,w_700,m_lfit)  

**队头阻塞和握手延迟是TCP的缺点，而TCP的优点是可靠传输。UDP和TCP进行互补就有了QUIC。**  

### 3.1 HTTP/3的特性

#### 3.1.1 连接迁移

TCP基于4要素（**源IP、源端口、目标IP、目标端口**）。  
切换网络时至少会有一个要素发生变化，导致连接发生变化。  
当连接发生变化时，如果还使用原来的TCP连接，则会导致连接失败，就得等原来的连接超时后重新建立连接。  
所以我们有时候发现切换到一个新网络时，即使新网络状况良好，但内容还是需要加载很久。  
如果实现的好，当检测到网络变化时立刻建立新的TCP连接，即使这样，建立新的连接还是需要几百毫秒的时间。  

QUIC的连接不受4要素的影响，当4要素发生变化时，原连接依然维持。  
QUIC连接不以4要素作为标识，而是使用一组**Connection ID**（连接ID）来标识一个连接。  
即使IP或者端口发生变化，只要Connection ID没有变化，那么连接依然可以维持。  
比如当设备连接到Wi-Fi时，将进行中的下载从蜂窝网络连接转移到更快速的Wi-Fi连接，当Wi-Fi连接不再可用时，将连接转移到蜂窝网络连接。  

#### 3.1.2 操作系统内核、CPU负载

据Google和Facebook称，与基于TLS的HTTP/2相比，它们大规模部署的QUIC需要近2倍的CPU使用量。  
因为Linux内核的UDP部分没有得到像TCP那样的优化，因为传统上没有使用UDP进行如此高速的信息传输。  
TCP和TLS有硬件加速，而这对于UDP很罕见，对于QUIC则基本不存在。  

随着时间的推移，相信这个问题会逐步得到改善。  

### 3.2 疑问

* HTTP/3基于UDP，如何保证可靠传输？  
    由QUIC来保证（在QUIC中加入了保证可靠传输的算法）  

* 为何Google不开发一个新的不同于TCP、UDP的传输层协议？  
    首先Google是绝对有能力开发的，主要是目前世界上的网络设备基本只认TCP、UDP。  如果要修改传输层，意味着操作系统的内核也要修改。  
    另外，由IETF标准化的需要TCP新特性都因缺乏广泛支持而没有得到广泛的部署或使用。  
    因此，要想开发并应用一个新的传输层协议，是极其困难的一件事情。  

**HTTP/3还在优化中（不成熟），所以现在大部分公司还是使用的HTTP/2或者HTTP/1.1。**  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
