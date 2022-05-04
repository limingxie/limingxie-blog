---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTPS协议简介(10)"
date: 2022-05-06T18:05:49+08:00
tags: [
    "WebSocket",
    "FTP",
    "RESTful",
    "SMTP",
    "IMAP",
    "POP",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了WebSocket、FTP、RESTful、SMTP、IMAP、POP等协议。  

## 1.ARP/RARP/ICMP

**ARP（Address Resolution Protocol）**，地址解析协议。  

* 通过IP地址获取MAC地址。  

**RARP（Reverse Address Resolution Protocol）**，逆地址解析协议。  

* 使用与ARP相同的报头结构  
* 作用与ARP相反，用于将MAC地址转换为IP地址  
* 后来被BOOTP、DHCP所取代  

**ICMP（Internet Control Message Protocol）**，互联网控制消息协议。  

* IPV4中的ICMP被称作ICMPv4，IPV6中的ICMP则被称作ICMPv6  
* 通常用于返回错误信息，比如TTL值过期，目的不可达  
* ICMP的错误消息总是包括了源数据并返回给发送者  

## 2.WebSocket

WebSocket是基于TCP的支持全双工通信的应用层协议（全双工：客户端和服务器均可主动发消息给对方）。  
在2011年由IETF标准化为[RFC_6455](https://datatracker.ietf.org/doc/html/rfc6455)，后由RFC_7936补充规范。  

WebSocket的应用场景很多：社交订阅、股票基金报价、体育实况更新、多媒体聊天、多玩家游戏等。  

> Socket是一套网络编程API，几乎所有的网络应用协议（HTTP/FTP/SMTP等）都是基于Socket实现的。  
> 而WebSocket是Web端的一个应用层通信协议。  

### 2.1 HTTP和WebSocket

HTTP请求的特点：通信只能由客户端发起，所以早期很多网站为了实现推送技术，使用的技术都是轮询。  

轮询是指由浏览器每隔一段时间（如每秒）向服务器发出HTTP请求，然后服务器返回最新的数据给客户端。  
为了能更好的节省服务器资源和带宽，并且能够更实时地进行通讯，HTML5规范中出现了WebSocket协议。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_121.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_121.png?x-oss-process=image/resize,w_700,m_lfit)  

WebSocket和HTTP属于平级关系，都是应用层的协议。其实TCP本身就是支持全双工通信的，只是HTTP的“请求-应答”模式限制了TCP的能力。  

WebSocket使用80（ws://）、443（wss://）端口，  
可以绕过大多数防火墙的限制（http默认使用80端口，https默认使用443端口）。  

与HTTP不同的是，WebSocket需要先建立连接（应用层连接）。  
这就使得WebSocket成为一种有状态的协议，之后通信时可以省略部分状态信息。而HTTP请求可能需要在每个请求都额外携带状态信息（如身份认证等）。  

### 2.2 建立连接

WebSocket需要借助HTTP协议来建立连接（也叫作握手，Handshake）。  

由客户端（浏览器）主动发出握手请求：  

```html
GET /chat HTTP/1.1  
Host: server.example.com  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
Origin: <http://example.com>  
Sec-WebSocket-Protocol: chat, superchat  
Sec-WebSocket-Version: 13  
```

服务器响应：

```html
HTTP/1.1 101 Switching Protocols  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=  
Sec-WebSocket-Protocol: chat  
```

**Connection**：必须设置Upgrade，表示客户端希望连接升级。  
**Upgrade**：必须设置websocket，表示希望升级到WebSocket协议。  
**Sec-WebSocket-Version**：表示支持的WebSocket版本，RFC_6455要求使用的版本是13。  
**Sec-WebSocket-Key**：客户端生成的随机字符串（比如例子中的dGhlIHNhbXBsZSBub25jZQ==）。  

服务器接收到客户端的Sec-WebSocket-Key后，会进行以下操作：  

1. Sec-WebSocket-Key拼接一个固定的GUID值（258EAFA5-E914-47DA-95CA-C5AB0DC85B11）。  
例：dGhlIHNhbXBsZSBub25jZQ==258EAFA5-E914-47DA-95CA-C5AB0DC85B11。  
2. 将1的结果进行SHA-1摘要计算，结果：b37a4f2cc0624f1690f64606cf385945b2bec4ea。  
3. 将2的结果进行Hex to Base64编码，结果：s3pPLMBiTxaQ9kYGzzhZRbK+xOo=。  
4. 将3的结果作为Sec-WebSocket-Accept响应头的值，返回给客户端。  

如此操作，可以尽量避免普通HTTP请求被误认为WebSocket协议。  

### 2.3 使用

WebSocket体验和演示：<https://www.websocket.org/echo.html>  
W3C标准化了一套WebSocket API，可以直接使用JS调用。  

```html
let ws = new WebSocket('wss://example.com')  
```

## 3.WebService

WebService（Web服务）是一种跨编程语言和跨操作系统平台的远程调用技术标准。  

WebService使用场景举例：  

* 天气预报、手机归属地查询、航班信息查询、物流信息查询等。  
* 比如天气预报，是气象局把自己的服务以WebService形式暴露出来，让第三方程序可以调用这些服务功能（<http://www.webxml.com.cn/zh_cn/index.aspx>）。

事实上，WebService完全可以用普通的Web API取代（比如HTTP + JSON），现在很多企业的开放平台都是直接采用Web API。  

### 3.1 WebService - 核心概念

**SOAP（Simple Object Access Protocol）**，简单对象访问协议。  
很多时候，SOAP = HTTP + XML。WebService使用SOAP协议来封装传递数据。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_122.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_122.png?x-oss-process=image/resize,w_700,m_lfit)  

**SOAP其实就是正常的HTTP请求和有规范格式的XML，请求和响应都是XML文件。**  

WSDL（Web Services Description Language），Web服务描述语言。  
一个XML文档，用以描述WebService接口的细节（比如参数、返回值等）。  
一般在WebService的URL后面跟上?wsdl获取WSDL信息。  
比如：<http://ws.webxml.com.cn/WebServices/WeatherWS.asmx?wsdl>  

## 4.RESTful

**REST（Representatioanl State Transfer）**，表现层状态转移。  

REST是一种互联网软件架构设计风格，定义了一组用于创建Web服务的约束。  
符合REST架构的Web服务，称为RESTful Web服务器。  

> RESTful后面的ful表示形容词，例如wonder -> wonderful 。  

**实践建议:**  

* URL中使用名词（建议用复数形式），不使用动词。  
    推荐：/users、/users/6  
    不推荐：listUsers、/getUser?id=6、/user/list、/user/get?id=6  

* 使用HTTP的方法表达动作：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_123.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_123.png?x-oss-process=image/resize,w_700,m_lfit)  

* 一个资源连接到其他资源，使用子资源的形式  
    GET：/users/6/cars/88  
    POST：/users/8/cars  

* API版本化  
    idbeny.com/v1/users  
    idbeny.com/v2/users/66  

* 返回JSON格式的数据  
* 发生错误时，不要返回200状态码  

## 5.TTPDNS

HTTPDNS是基于HTTP协议向DNS服务器发送域名解析请求。  
替代了基于DNS协议向运营商Local DNS发起解析请求的传统方式，可以避免Local DNS造成的域名劫持和跨网访问问题。  
常用在移动互联网中（比如Android、iOS开发中）。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_124.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_124.png?x-oss-process=image/resize,w_700,m_lfit)  

市面上已经有现成的解决方案，移动端集成相关的SDK即可使用HTTPDNS服务。  

腾讯云：<https://cloud.tencent.com/product/httpdns>  
阿里云：<https://help.aliyun.com/product/30100.html>  

## 6.FTP

**FTP（File Transport Protocol）**，文件传输协议。  
[RFC_959](https://datatracker.ietf.org/doc/html/rfc959)定义了此规范，是基于TCP的应用层协议。  
在[RFC_1738](https://datatracker.ietf.org/doc/html/rfc1738)中有定义，FTP的URL格式为：<ftp://[user[:password>]@]host:[:port]/url-path。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_125.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_125.png?x-oss-process=image/resize,w_700,m_lfit)  

### 6.1 连接模式

FTP有2种连接模式：主动（Active）和被动（Passive）。  
不管是哪种方式，都需要客户端和服务器建立2个连接：  

* 控制连接：用于传输状态信息（指令）  
* 数据连接：用于传输文件和目录信息（数据）  

[图片备用地址](https://limingxie.github.io/images/network/application/application_126.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_126.png?x-oss-process=image/resize,w_700,m_lfit)  

#### 6.1.1 主动模式

主动模式是服务器主动，客户端被动。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_127.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_127.png?x-oss-process=image/resize,w_700,m_lfit)  

* 客户端打开一个随机的命令端口  
    端口号大于1024，假设为N。同时连接至服务器的命令端口21（固定）  
* 客户端开始监听N+1数据端口  
    同时向服务器发送一个Port命令给服务器的命令端口21  
    此命令告诉服务器，客户端正在监听的数据端口是N+1，并且已准备好从此端口接收数据  
* 服务器打开20号数据端口（固定），并且创建和客户端数据端口（N+1）的连接  

#### 6.1.2 被动模式

被动模式是客户端主动，服务器被动。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_128.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_128.png?x-oss-process=image/resize,w_700,m_lfit)  

* 客户端通过两个随机的端口与服务器建立连接：命令端口N、数据端口N+1。  
    客户端的命令端口N用于连接服务器的命令端口21（固定）  
    客户端通过命令端口N发送PASV命令给服务器的命令端口21  
    服务器打开一个随机的数据端口P，并告知客户端该端口号是P  
    客户端数据端口N+1发起与服务器数据端口P的连接  

## 7.邮件相关的协议

* 发邮件使用的协议：  
  * SMTP（Simple Mail Transfer Protocol），简单邮件传输协议。  
    基于TCP，标准参考[RFC_5321](https://datatracker.ietf.org/doc/html/rfc5321)  
    服务器默认使用25端口，SSL/TLS使用465端口  

* 收邮件使用的协议：  
  * POP（Post Office Protocol），邮局协议。  
    基于TCP，最新版是POP3，标准参考[RFC_1939](https://datatracker.ietf.org/doc/html/rfc1939)  
    服务器默认使用110端口，SSL/TLS使用995端口  

  * IMAP（Internet Message Access Protocol），因特网信息访问协议。  
    基于TCP，最新版是IMAP4，标准参考[RFC_3501](https://datatracker.ietf.org/doc/html/rfc3501)  
    服务器默认使用143端口，SSL/TLS使用993端口  

### 7.1 收发邮件的过程

[图片备用地址](https://limingxie.github.io/images/network/application/application_129.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_129.png?x-oss-process=image/resize,w_700,m_lfit)  

不同邮箱服务是不同直接通信的，例如腾讯邮箱（@qq.com）要想发邮件给网易邮箱（@163.com），  
客户端通过SMTP协议发送至腾讯邮箱服务，腾讯邮箱服务器通过SMTP协议转发给网易邮箱服务器，  
网易邮箱用户通过POP/IMAP协议拉取网易邮箱服务器上的邮件。  

### 7.2 POP的特点

[图片备用地址](https://limingxie.github.io/images/network/application/application_130.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_130.png?x-oss-process=image/resize,w_700,m_lfit)  

* 客户端连接服务器时，将会从服务器下载所有邮件。  
    可以设置下载完后，立即或一段时间后删除服务器邮件。  
* 客户端的操作（比如删除邮件、移动到文件夹）不会跟服务器同步。  
* 每个客户端都是独立的，都可以获得其自己的电子邮件副本。  

### 7.3 IMAP的特点

[图片备用地址](https://limingxie.github.io/images/network/application/application_131.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_131.png?x-oss-process=image/resize,w_700,m_lfit)  

* 客户端连接服务器时，获取的是服务器上邮件的基本信息，并不会下载邮件。  
    等打开邮件时，才开始下载邮件。  
* 客户端的操作（比如删除邮件、移动到文件夹）会跟服务器同步。  
* 所有客户端始终会看到相同的邮件和相同的文件夹。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
