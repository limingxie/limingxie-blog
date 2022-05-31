---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTP协议简介(4)"
date: 2022-04-21T18:05:49+08:00
tags: [
    "OSI",
    "Status Code",
    "http",
    "code",
    "status",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了应用层的 HTTP协议的状态码和form表单。

## 1.状态码（Status Code）

状态码是在[RFC_2616,section-10:Status Code Definitions](https://datatracker.ietf.org/doc/html/rfc2616#section-10)规范中定义的。  
状态码指示HTTP请求是否已成功完成。  

### 1.1 状态码分类

状态码分为5类：

* 信息响应：100~199
* 成功响应：200~299
* 重定向：300~399
* 客户端错误：400~499
* 服务器错误：500~599

### 1.2 常见状态码

#### 100（Continue）

请求的初始化部分已经被服务器收到，并且没有被服务器拒绝。  
客户端应该继续发送剩余的请求，如果请求已经完成，就忽略这个响应。  

应用场景：允许客户端发送带请求体的请求前，判断服务器是否愿意接收请求（服务器通过请求头判断）。  
在某些情况下，如果服务器在不看请求体就拒绝请求时，客户端就发送请求体是不恰当的或低效的。  

#### 200（OK）

请求成功  

#### 302（Found）

请求的资源被暂时的移动到了由Location头部指定的URL上。  

#### 304（Not Modified）

说明无需再次传输请求的内容（也就是说客户端可以使用之前302缓存的内容，服务器只返回响应头）。  

#### 400（Bad Request）

由于语法无效，服务器无法理解该请求。  

#### 401（Unauthorized）

由于缺乏目标资源要求的身份验证凭证。  

#### 403（Forbidden）

服务端有能力处理该请求，但是拒绝授权访问。  

#### 404（Not Found）

服务器端无法找到所请求的资源。  

#### 405（Method Not Allowed）

服务器禁止了使用当前HTTP方法的请求（比如应该使用POST请求而客户端使用的是GET请求）。  

#### 406（Not Acceptable）

服务器端无法提供与Accept-Charset以及Accept-Language指定的值相匹配的响应。  

#### 408（Request Timeout）

服务器想要将没有在使用的连接关闭（一些服务器会在空闲连接上发送此信息，即便在客户端没有发送任何请求的情况下）。  

#### 500（Internal Server Error）

所请求的服务器遇到意外的情况并阻止其执行请求。

#### 501（Not Implemented）

请求的方法不被服务器支持，因此无法被处理。  
服务器必须支持的方法（即不会返回这个状态码的方法）只有GET和HEAD。  
注意和405的区分：405代表服务器支持该请求方法，只是客户端使用了错误的请求方法；而501是服务器不支持该请求方法。  

#### 502（Bad Gateway）

作为网关或代理角色的服务器，从上游服务器（如tomcat）中接收到的响应是无效的。

#### 503（Service Unavailable）

服务器尚未处于可以接受请求的状态。通常造成这种情况的原因是由于服务器停机维护或者已超载。

## 2.form（表单）提交

常用属性：

* action：请求的URI
* method：请求方法（只支持GET和POST）
* enctype：POST请求时，请求体的编码方式
  * application/x-www-form-urlencoded，默认值。用&分割参数，用=分割键和值，字符用URL编码方式进行编码
  * multipart/form-data：文件上传时必须使用这种编码方式

multipart/form-data参考[RFC_1521](https://datatracker.ietf.org/doc/html/rfc1521)。

#### 请求头

```html
Content-Type: multipart/form-data; boundary=xxx
```

#### 请求体

```html
multipart-body := preamble 1*encapsulation
               close-delimiter epilogue

encapsulation := delimiter body-part CRLF

delimiter := "--" boundary CRLF ; taken from Content-Type field.
                                ; There must be no space
                                ; between "--" and boundary.

close-delimiter := "--" boundary "--" CRLF ; Again, no space
   by "--",

preamble := discard-text   ;  to  be  ignored upon receipt.

epilogue := discard-text   ;  to  be  ignored upon receipt.
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
