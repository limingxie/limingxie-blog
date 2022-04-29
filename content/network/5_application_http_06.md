---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTP协议简介(6)"
date: 2022-05-02T18:05:49+08:00
tags: [
    "OSI",
    "Proxy Server",
    "CND",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了代理，CND，网络安全相关的内容。  

## 1.代理

[图片备用地址](https://limingxie.github.io/images/network/application/application_31.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_31.png?x-oss-process=image/resize,w_700,m_lfit)  

### 1.1 代理服务器（Proxy Server）  

特点： 本身不生产内容，处于中间位置转发上下游的请求和响应。  

面向下游的客户端：它是服务器（正向代理）  
面向上游的服务器：它是客户端（反向代理）  

[图片备用地址](https://limingxie.github.io/images/network/application/application_32.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_32.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 1.1.1 正向代理  

代理的对象是客户端。  
作用：  

* **隐藏客户端身份**  
    网络请求使用的是代理服务器IP，所以不会暴露客户端IP（但实际上是可以获取的，你懂得！！！）

* **绕过防火墙（突破访问限制）**  
    比如经常使用的科学上网工具就是干这个的  

* **Internet访问控制**  
    路由器设置指定服务器允许上网，其他客户端如果想要上网只能通过指定的服务器，  
    而这台服务器通过判断客户端IP决定其是否能够交互数据，这台服务器就是代理服务器  

* **数据过滤**  
    代理服务器通过规则解析数据内容，决定数据是否过滤  
    ... ...

[图片备用地址](https://limingxie.github.io/images/network/application/application_33.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_33.png?x-oss-process=image/resize,w_700,m_lfit)  

免费的正向代理：  
<https://ip.jiangxianli.com/>  
<https://www.kuaidaili.com/free/inha>  

#### 1.1.2 反向代理  

代理的对象是服务器。  
作用：  

* **隐藏服务器身份**  
* **安全防护**  
* **负载均衡**  
......

[图片备用地址](https://limingxie.github.io/images/network/application/application_34.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_34.png?x-oss-process=image/resize,w_700,m_lfit)  

上图中的负载均衡服务器就是一个反向代理，为服务器服务的。  
当用户访问指定服务器时，数据会通过负载均衡服务器进行转发。  
这样不仅可以隐藏真实服务器的身份，还能通过一些算法让服务器稳定运行。  

#### 1.1.3 代理服务器相关的头部字段  

**Via:** 追加经过的每一台代理服务器的主机名（或域名）  
**X-Forwarded-For:** 追加请求方的IP地址  
**X-Real-IP:** 客户端的真实IP地址  

[图片备用地址](https://limingxie.github.io/images/network/application/application_35.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_35.png?x-oss-process=image/resize,w_700,m_lfit)  

① => **Via**：代理1 **X-Forwarded-For**：14.14.14.14 **X-Real-IP**：14.14.14.14  

② => **Via**：代理1、代理2 **X-Forwarded-For**：14.14.14.14、220.11.11.11 **X-Real-IP**：14.14.14.14  

③ => **Via**：代理2  

④ => **Via**：代理2、代理1  

## 1.2 抓包工具的原理  

Fiddler、Charles等抓包工具的原理：在客户端启动了正向代理服务。  
Wireshark不是代理，它的原理是：通过底层驱动，拦截网卡上流过的数据。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_36.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_36.png?x-oss-process=image/resize,w_700,m_lfit)  

## 2.CDN  

**CDN（Content Delivery Network 或 Content Distribution Network）**，译为：内容分发网络。  
**作用:** 利用最靠近每位用户的服务器，更快更可靠的将音乐、图片、视频等资源文件（一般是静态资源）传递给用户。  

CDN出现之前，所有的内容都是从一台服务器取：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_37.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_37.png?x-oss-process=image/resize,w_700,m_lfit)  

CDN出现之后，内容就从距离用户最近的一台服务器取：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_38.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_38.png?x-oss-process=image/resize,w_700,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_39.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_39.png?x-oss-process=image/resize,w_700,m_lfit)  

对比：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_40.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_40.png?x-oss-process=image/resize,w_700,m_lfit)  

CDN运营商在全国、乃至全球的各大枢纽城市都建立了机房。  
部署了大量拥有高存储高带宽的节点，构建了一个跨运营商、跨地域的专用网络。  
内容所有者向CDN运营商支付费用，CDN将其内容交付给最终用户。  

举例，使用CDN引入jQuery：

```script
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
    $(() => {
        $(document.body).css('background', '#f00')
    })
</script>
```

**不是域名包含CDN就是CDN服务器。**  

## 3.网络安全  

网络通信中面临的4种安全威胁：  

**截获:** 窃听通信内容  
**中断:** 中断网络通信  
**篡改:** 篡改通信内容  
**伪造:** 伪造通信内容  

[图片备用地址](https://limingxie.github.io/images/network/application/application_41.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_41.png?x-oss-process=image/resize,w_700,m_lfit)  

### 3.1 网络层-ARP欺骗  

ARP欺骗（ARP spoofing），又称ARP毒化（ARP poisoning）、ARP病毒、ARP攻击。  
ARP欺骗可以造成的效果：  

* 可让攻击者获取局域网上的数据包甚至可篡改数据包  
* 可让网络上特定电脑之间无法正常通信（例如网络执法官这样的软件）  
* 让送至特定IP地址的流量被错误送到攻击者所取代的地方  
* ......  

**ARP欺骗核心步骤举例:**  

* 假设主机C是攻击者，主机A、B是被攻击者  
* C只要收到过A、B发送的ARP请求，就会拥有A、B的IP、MAC地址，就可以进行欺骗活动  
* C发送一个ARP响应给B，把响应包里的源IP设为A的IP地址，源MAC设为C的MAC地址  
* B收到ARP响应后，更新它的ARP表，把A的MAC地址（IP_A、MAC_A）改为（IP_A、MAC_C）  
* 当B要发送数据包给A时，它根据ARP表来封装数据包的头部，把目标MAC地址设为MAC_C、而非MAC_A  
* 当交换机收到B发送给A的数据包时，根据此包的目标MAC地址（MAC_C）而把数据包转发给C  
* C收到数据包后，可以把它存起来后再发送给A，达到窃听效果。C也可以篡改数据后才发送数据包给A  

**ARP欺骗的防护**  

* 使用静态ARP  
* 借助DHCP Snooping  
    网络设备可借由DHCP保留网络上各电脑的MAC地址，在伪造的ARP数据包发出时即可侦测到  
* 利用一些软件监听ARP的不正常变动  
* ......  

### 3.2 DoS、DDoS  

**DoS攻击（拒绝服务攻击，Denial-of-Service attack）**，使目标电脑的网络或系统资源耗尽，使服务暂时中断或停止，导致其正常用户无法访问。  

Dos攻击可以分为2大类：  

* 带宽消耗型：UDP洪水攻击、ICMP洪水攻击  
    不断向服务器使用UDP或ICMP传输数据，占满其带宽，导致服务器访问过载直至宕机。  
* 资源消耗型：SYN洪水攻击、LAND攻击  

**DDoS（分布式拒绝服务攻击，Distributed Denial-of-Service attack）**。  
黑客使用网络上两个或以上被攻击的电脑作为“僵尸”向特定的目标发动DoS攻击。  

> 2018年3月，GitHub遭到迄今为止规模最大的DDoS攻击。  

#### 3.2.1 传输层-SYN洪水攻击  

**SYN洪水攻击（SYN flooding attack）**。  
攻击者发送一系列的SYN请求到目标，然后让目标因收不到ACK（第3次握手）而进行等待，消耗资源。  

攻击方法：  

* 跳过发送最后的ACK信息  
* 修改源IP地址，让目标送SYN-ACK到伪造的IP地址，因此目标永不可能收到ACK（第3次握手）  

防护：参考RFC_4987  

#### 3.2.2 传输层-LAND攻击  

**LAND攻击（局域网拒接服务攻击，Local Area Network Denial attack）**。  
通过持续发送相同源地址和目标地址的欺骗数据包，使目标视图与自己建立连接，消耗系统资源直至崩溃。  
有些系统存在设计上的缺陷，允许设备接受并响应来自网络、却宣称来自于设备自身的数据包，导致循环应答。  

防护：  

* 大多数防火墙都能拦截类似的攻击包，以保护系统  
* 部分操作系统通过发布安全补丁修复了这一漏洞  
* 路由器应同时配置上行与下行筛选器，屏蔽所有源地址与目标地址相同的数据包  

### 3.3 DoS、DDoS防御  

防御方式通常为：入侵检测、流量过滤、多重验证。堵塞网络带宽的流量将被过滤，而正常的流量可正常通过。  

**防火墙:** 防火墙可以设置规则，例如允许或拒绝特定通讯协议，端口或IP地址。  

* 当攻击从少数不正常的IP地址发出时，可以简单的使用拒绝规则阻止一切从攻击源IP发出的通信  
* 复杂攻击难以用简单规则来阻止，例如80端口遭受攻击时不可能拒绝端口所有的通信，因为同时会阻止合法流量  
* 防火墙可能处于网络架构中过后的位置，路由器可能在恶意流量达到防火墙前即被攻击影响  

**交换机:** 大多数交换机有一定的速度限制和访问控制能力。  
**路由器:** 和交换机类似，路由器也有一定的速度限制和访问控制能力。  
**黑洞引导:** 将所有受攻击计算机的通信全部发送至一个“黑洞”（空接口或不存在的计算机地址）或者有足够能力处理洪流的网络设备商，以避免网络受到较大影响。  
**流量清洗:** 当流量被送到DDoS防护清洗中心时，通过采用抗DDoS软件处理，将正常流量和恶意流量区分开。正常的流量则回注回客户网站。  

### 3.4 应用层-DNS劫持  

DNS劫持，又称为域名劫持。攻击者篡改了某个域名的解析结果，使得指向该域名的IP变成了另一个IP。导致对应网址的访问被劫持到另一个不可达的或者假冒的网址。从而实现非法窃取用户信息或者破坏正常网络服务的目的。  
为防止DNS劫持，可以考虑使用更靠谱的DNS服务器，比如：114.114.114.114。  

* 谷歌：8.8.8.8，8.8.4.4  
* 微软：4.2.2.1，4.2.2.2  
* 百度：180.76.76.76  
* 阿里：223.5.5.5，223.6.6.6  

### 3.5 应用层-HTTP劫持  

HTTP劫持是对HTTP数据包进行拦截处理，比如插入JS代码。  
最常见的是在访问某些网站时，在右下角多了个莫名其妙的弹窗广告。  

### 3.6 HTTP协议的安全问题  

HTTP协议默认是采用明文传输的，因此会有很大的安全隐患。常见的提高安全性的方法是：对通信内容进行加密后，再进行传输。  
常见的加密方式：  

* 不可逆  
    单向散列函数：MD5、SHA等  

* 可逆  
    对称加密：DES、3DES、AES等  
    非对称加密：RSA等  

* 其他  
    混合密码系统  
    数字签名  
    证书  

**常见英文：encrypt（加密）、decrypt（解密）、plaintext（明文）、ciphertext（密文）**。  

### 3.7 如何防止被窃听？  

为了便于区分，设计4个虚拟人物：  
Alice、Bob：相互通信  
Eve：窃听者  
Mallory：主动攻击者  

[图片备用地址](https://limingxie.github.io/images/network/application/application_42.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_42.png?x-oss-process=image/resize,w_700,m_lfit)  

加密：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_43.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_43.png?x-oss-process=image/resize,w_700,m_lfit)  

常见的加解密地址：  
MD5加密：<https://www.cmd5.com/hash.aspx>  
MD5解密：<https://www.cmd5.com/>  
其他加密：<https://www.sojson.com/encrypt_des.html>  
<https://tool.chinaz.com/tools/md5.aspx>  

### 3.8 单向散列函数（One-way hash function）  

单向散列函数，可以根据消息内容计算出散列值。  
单向散列函数也被称为：消息摘要函数（message digest function）、哈希函数（hash function）。  
输出的散列值也被称为：消息摘要（message digest）、指纹（fingerprint）  
散列值的长度和消息的长度无关，无论消息是1bit、10M、100G，单向散列函数都会计算出固定长度的散列值。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_44.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_44.png?x-oss-process=image/resize,w_700,m_lfit)  

特点：  

* 根据任意长度的消息，计算出固定长度的散列值  
* 计算速度快，能快速计算出散列值  
* 消息不同，散列值也不同  
* 具备单向性  

[图片备用地址](https://limingxie.github.io/images/network/application/application_45.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_45.png?x-oss-process=image/resize,w_700,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_46.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_46.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 3.8.1 常见的几种单向散列函数  

* **MD4、MD5**  
产生128bit的散列值，MD就是Message Digest的缩写，目前已经不安全  

* **SHA-1**  
产生168bit的散列值，目前已经不安全  

* **SHA-2**  
SHA-256、SHA-384、SHA-512，散列值长度分别是256bit、384bit、512bit  

* **SHA-3**  
全新标准  

#### 3.8.2 如何防止数据被篡改？  

如何确保两个文件是一样的？  

[图片备用地址](https://limingxie.github.io/images/network/application/application_47.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_46.png?x-oss-process=image/resize,w_700,m_lfit)  

原始做法就是拷贝一份进行比较（非常笨重）。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_48.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_47.png?x-oss-process=image/resize,w_700,m_lfit)  

计算比较散列值（推荐）。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_49.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_48.png?x-oss-process=image/resize,w_700,m_lfit)  

在下载软件的时候，有些网站就会提供软件包的哈希值（比如：IDEA），防止软件包被非法篡改，  
我们就可以在镜像网站（速度快）下载安装包后计算出哈希值，然后和官方软件包的哈希值做比较，以确保软件完整性。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_50.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_50.png?x-oss-process=image/resize,w_700,m_lfit)  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
