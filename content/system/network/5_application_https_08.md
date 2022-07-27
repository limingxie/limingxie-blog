---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTPS协议简介(8)"
date: 2022-04-25T18:05:49+08:00
tags: [
    "OSI",
    "https",
    "code",
    "status",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了HTTPS的TLS协议。  
<!--more-->
HTTPS由网景公司于1994年首次提出。  
HTTPS是在HTTP的基础上使用SSL/TLS来加密报文，对窃听和中间人攻击提供合理的防护。  

## 1.HTTPS

**HTTPS（HyperText Transfer Protocol Secure）**，超文本传输安全协议。常称为HTTP over TLS、HTTP over SSL、HTTP Secure。  
HTTPS的**默认端口号是443**（HTTP是80）。

[图片备用地址](https://limingxie.github.io/images/network/application/application_80.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_80.png?x-oss-process=image/resize,w_700,m_lfit)  

在浏览器中输入<http://www.baidu.com>，  
会自动跳转到<https://www.baidu.com>，  
这个跳转指向不是浏览器自己决定的，是访问<http://www.baidu.com>>后，所在服务器返回了重定向地址。

[图片备用地址](https://limingxie.github.io/images/network/application/application_81.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_81.png?x-oss-process=image/resize,w_700,m_lfit)  

如果不加协议头，浏览器默认域名使用HTTPS。  

HTTPS的成本：证书的费用、加解密的计算等、降低了访问速度。  
有些企业的做法是：包含敏感数据的请求才使用HTTPS，其他仍让使用HTTP，  
例如工商银行（<http://www.icbc.com.cn、https://mybank.icbc.com.cn>）。

HTTPS的通信过程，总的可以分为3大阶段：  

* TCP的3次握手
* TLS的连接
* HTTP请求和响应

[图片备用地址](https://limingxie.github.io/images/network/application/application_82.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_82.png?x-oss-process=image/resize,w_700,m_lfit)  

## 2.SSL/TLS

**TLS（Transport Layer Security）**，传输层安全性协议。  
前身是**SSL（Secure Sockets Layer）**，安全套接层。  

SSL/TLS也可以用在其他协议上，比如FTP -> FTPS、SMTP -> SMTPS。  

### 2.1 历史版本信息

SSL 1.0：因存在严重的安全漏洞，从未公开过  
SSL 2.0：1995年，已于2011年弃用 [参考RFC_6176](https://datatracker.ietf.org/doc/html/rfc6176)  
SSL 3.0：1996年，已于2015年弃用 [参考RFC_7568](https://datatracker.ietf.org/doc/html/rfc7568)  
TLS 1.0：1999年，[参考RFC_2246](https://datatracker.ietf.org/doc/html/rfc2246)  
TLS 1.1：2006年，[参考RFC_4346](https://datatracker.ietf.org/doc/html/rfc4346)  
TLS 1.2：2008年，[参考RFC_5246](https://datatracker.ietf.org/doc/html/rfc5246)  
TLS 1.3：2018年，[参考RFC_8446](https://datatracker.ietf.org/doc/html/rfc8446)  

### 2.2 OSI

SSL/TLS是在应用层和传输层之间，因为SSL是为HTTP的数据包进行加密的，所在必须在数据包到达传输层前介入。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_83.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_83.png?x-oss-process=image/resize,w_700,m_lfit)  

### 2.3 OpenSSL

OpenSSL是SSL/TLS协议的开源实现，始于1998年，支持Windows、Mac、Linux等平台。Linux、Mac一般自带OpenSSL，Windows需要下载安装（地址：<https://slproweb.com/products/Win32OpenSSL.html>）。  

可以使用OpenSSL构建一套属于自己的CA，自己给自己颁发证书，称为“自签名证书”。  

### 2.4 TLS 1.2 的连接

[图片备用地址](https://limingxie.github.io/images/network/application/application_94.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_94.png?x-oss-process=image/resize,w_700,m_lfit)  

大概有10大步骤，图片中省略了中间产生的一些ACK确认。  

#### 第一步：Client Hello

* TLS的版本号
* 支持的加密组件列表（Cipher Suite）
    加密组件是指所使用的加密算法及密钥长度等
* 一个随机数（Client Random）

[图片备用地址](https://limingxie.github.io/images/network/application/application_84.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_84.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第二步：Server Hello

* TLS的版本号
* 选择的加密组件
    是从接收到的客户端加密组件列表中挑选出来的
* 一个随机数（Server Random）

[图片备用地址](https://limingxie.github.io/images/network/application/application_85.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_85.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第三步：Certificate

* 服务器的公钥证书（被CA签名过的）

[图片备用地址](https://limingxie.github.io/images/network/application/application_86.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_86.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第四步：Server Key Exchange

* 用以实现ECDHE算法的其中一个参数（Server Params）
    ECDHE是一种密钥交换算法
    为了防止伪造，Server Params经过了服务器私钥签名

[图片备用地址](https://limingxie.github.io/images/network/application/application_95.jpg)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_95.jpg?x-oss-process=image/resize,w_700,m_lfit)  

#### 第五步：Server Hello Done

* 告知客户端：协商部分结束

[图片备用地址](https://limingxie.github.io/images/network/application/application_87.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_87.png?x-oss-process=image/resize,w_700,m_lfit)  

到第五步为止，客户端和服务器之间都是通过明文共享了：Client Random、Server Random、Server Params。  
而且客户端也拿到了服务器的公钥证书，接下来客户端会验证证书的真实有效性。

#### 第六步：Client Key Exchange

* 用以实现ECDHE算法的另一个参数（Client Params）

[图片备用地址](https://limingxie.github.io/images/network/application/application_88.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_88.png?x-oss-process=image/resize,w_700,m_lfit)  

到目前为止，客户端和服务器都拥有了ECDHE算法需要的2个参数：Server Params、Client Params。  
此时客户端和服务器都可以使用ECDHE算法，根据Server Params、Client Params计算出一个新的随机密钥串（Pre-master secret）。  
然后结合Client Random、Server Random、Pre-master secret生成一个主密钥，  
最后利用主密钥衍生出其他密钥：客户端发送用的会话密钥、服务器发送用的会话密钥等。

#### 第七步：Change Cipher Spec

* 告知服务器：之后的通信会采用计算出来的客户端会话密钥进行加密。

[图片备用地址](https://limingxie.github.io/images/network/application/application_89.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_89.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第八步：Finished

包含连接至今全部报文的整体校验值（摘要），加密之后（用会话密钥进行加密）发送给服务器。  
这次握手协商是否成功，要以服务器是否能够正确解密该报文作为判定标准

[图片备用地址](https://limingxie.github.io/images/network/application/application_90.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_90.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第九步：Change Cipher Spec

服务器通知客户端，可以进行加密通信（服务器使用服务器会话密钥）。

[图片备用地址](https://limingxie.github.io/images/network/application/application_91.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_91.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 第十步：Finished

告知客户端：服务器收到的报文加密后的校验值。

[图片备用地址](https://limingxie.github.io/images/network/application/application_92.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_92.png?x-oss-process=image/resize,w_700,m_lfit)  

到此为止，客户端服务器都验证加密解密没问题，握手正式结束。

开始加密传输：

[图片备用地址](https://limingxie.github.io/images/network/application/application_93.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_93.png?x-oss-process=image/resize,w_700,m_lfit)  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
