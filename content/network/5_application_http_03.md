---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTP协议简介(3)"
date: 2022-04-20T18:05:49+08:00
tags: [
    "OSI",
    "ABNF",
    "request",
    "header",
    "response",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了应用层的 HTTP协议的ABNF格式。

## 1.ABNF

**ABNF（Augmented BNF）**，是BNF（Backus-Naur Form，译：巴克斯-瑙尔范式）的修改/增强版。  
在RFC_5234中表明ABNF用作internet中通信协议的定义语言。  
ABNF是最严谨的HTTP报文格式描述形式，脱离ABNF谈论HTTP报文格式，往往都是不严谨的。

关于HTTP报文格式的定义：  

旧版：[RFC_2616_4.HTTP Message](https://datatracker.ietf.org/doc/html/rfc2616#section-4)  
新版：[RFC_7230_3.Message Format](https://datatracker.ietf.org/doc/html/rfc7230#section-3)  
ABNF核心规则：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_09.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_09.png?x-oss-process=image/resize,w_900,m_lfit)  

### 1.1 报文格式

```
HTTP-message   = start-line
                 *( header-field CRLF )
                 CRLF
                 [ message-body ]

start-line     = request-line / status-line
```

* **start-line**：request-line代表请求报文（请求行），status-line代表响应报文（状态行）  
* **`*`**：0个或多个。2*表示至少2个，3*6表示3到6个  
* 每一个header-field后面都必须加上回车换行符CRLF，header-field整体后面也必须加上回车换行符CRLF  
* **()**：组成一个整体  
* **[]**：可选（请求体、响应体）  

### 1.2 request-line、status-line

start-line内部包含了空格，所以在报文格式中没有看到换行符CRLF。  

ABNF中的注释格式是：分号(;)+内容，例;这里描述的是注释内容。  

```html
// 请求行（例：GET /hello/ HTTP/1.1）  
// SP = Space（空格），DIGIT代表数字，具体可参考上面的ABNF核心规则  
request-line = method SP request-target SP HTTP-version CRLF  
// HTTP-version格式（例：HTTP/1.1）  
HTTP-version  = HTTP-name "/" DIGIT "." DIGIT  
// HTTP-name格式（%x48.54.54.50是HTTP的16进制ASCII码值）  
HTTP-name     = %x48.54.54.50 ; "HTTP", case-sensitive  

// 状态行（例：HTTP/1.1 200）（例：HTTP/1.1 200 OK）  
status-line = HTTP-version SP status-code SP reason-phrase CRLF  
// 状态码由3个数字组成（例：200、300、500等）  
status-code    = 3DIGIT  
// 状态描述，可以是Tab、空格、字符或obs文字，*代表是可选项（例：OK）  
reason-phrase  =*( HTAB / SP / VCHAR / obs-text )  
```

### 1.3 header-field、message-body

```html
// 请求头键值对（例：Host: localhost:8080）  
header-field   = field-name ":" OWS field-value OWS  
// 指定键名  
field-name     = token  
// 值（可有可无）  
field-value    = *( field-content / obs-fold )  
// 空格或Tab键（*代表该值可有可无，也就是说有OWS的地方可以没有空格也可以有多个空格）  
OWS = *( SP / HTAB )  

// 消息体（只要是字节就可以）  
message-body = *OCTET  
```

### 1.4 telnet

使用telnet可以直接面向HTTP报文与服务器交互，可以更清晰、直观的看到请求报文和响应报文的内容，也可以检验请求报文格式的正确与否。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_10.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_10.png?x-oss-process=image/resize,w_800,m_lfit)  

**Windows系统用户可以使用Xshell软件**  
**Mac系统用户直接命令安装（brew install telnet）。**  

## 2.URL编码

URL中一旦出现了一些特殊字符（比如中文、空格），需要进行编码。在浏览器地址栏输入URL时，是采用UTF-8进行编码。  
比如<https://www.baidu.com/s?wd=你好编码后就是https://www.baidu.com/s?wd=%E4%BD%A0%E5%A5%BD>。  

## 3.请求方法

[RFC_7231,section-4: Request methods](https://datatracker.ietf.org/doc/html/rfc7231#section-4)：描述了8种请求方法。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_12.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_12.png?x-oss-process=image/resize,w_800,m_lfit)  

在[RFC_5789,section-2: Patch methods](https://tools.ietf.org/html/rfc5789#section-2)文档中描述了PATCH方法。  

所以到目前为止一共有9种HTTP请求方法。最常用的请求方法是GET和POST。  

* **GET**：常用于读取的操作，请求参数直接拼接在URL的后面（浏览器或服务器对URL是有长度限制的，ABNF并没有限制URL长度的说明）  
* **POST**：常用于添加、修改、删除的操作，请求参数可以放到请求体中（没有大小限制）  
* **HEAD**：请求得到与GET请求相同的响应，但没有响应体  
    使用场景举例：在下载一个大文件前，先获取其大小，再决定是否要下载，以此可以节约带宽资源  
* **OPTIONS**：用于获取目的资源所支持的通信选项，比如服务器支持的请求方法  
    使用telnet连接服务器后输入OPTIONS *HTTP/1.1（*代表所有路径），返回Allow: GET, HEAD, POST, PUT, DELETE, OPTIONS  
* **PUT**：用于对已存在的资源进行整体覆盖（很少使用，因为是直接覆盖数据，所以不太安全）  
* **DELETE**：用于删除指定的资源（很少使用，不安全）  
* **PATCH**：用于对资源进行部分修改（资源不存在，会创建新的资源。很少使用，不安全）  
* **CONNECT**：可以开启一个客户端与所请求资源之间的双向沟通的通道，它可以用来创建隧道（tunnel）  
    可以用来访问采用了SSL（HTTPS）协议的站点  
* **TRACE**：请求服务器回显其收到的请求信息（请求什么内容就响应什么内容），主要用于HTTP请求的测试或诊断  

## 4.请求头字段

头部字段（Header Field）可以分为4种类型：  

* **请求头字段（Request Header Fields）**  
    有关要获取的资源或客户端本身信息的消息头  
* **响应头字段（Response Header Fields）**  
    有关响应的补充信息，比如服务器本身（名称和版本等）的消息头  
* **实体头字段（Entity Header Fields）**  
    有关实体主体的更多信息，比如主体长度（Content-Length）或其MIME类型  
* **通用头字段（General Header Fields）**  
    同时适用于请求和响应消息，但与消息主体无关的消息头，比如时间（Date）  

[图片备用地址](https://limingxie.github.io/images/network/application/application_13.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_13.png?x-oss-process=image/resize,w_1000,m_lfit)  

```html
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
```

上面请求头的接收类型包含了很多种，每一种使用逗号,隔开，q=0.8是权重的意思，权重值越大，优先级越高（优先返回哪一种类型）。如果不指定q值，默认是1.0（最大值）。  

## 5.响应头字段

[图片备用地址](https://limingxie.github.io/images/network/application/application_14.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_14.png?x-oss-process=image/resize,w_1000,m_lfit)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
