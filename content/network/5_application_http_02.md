---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTP协议简介(2)"
date: 2022-04-27T09:05:49+08:00
tags: [
    "OSI",
    "http",
    "network",
]
categories: [
    "network",
]
---

## 1.HTTP

**HTTP（Hyper Text Transfer Protocol）**，  
超文本传输协议。是互联网中应用最广泛的应用层协议之一。

设计HTTP最初的目的是：  
提供一种发布和接收HTML页面的方法（HTTP最初就是用来传输HTML的），由URI来标识具体的资源。  
后来HTTP来传递的数据格式不仅仅是HTML，应用非常广泛。  
URL是URI的子级，URI是统一资源标识符，在服务器中是唯一的。URL是统一资源定位符，是为了定位资源位置的。  
可以参考我之前写的[URI,URL,URN的区别](https://limingxie.github.io/basic/uri_url_urn/)。

**HTML（Hyper Text Markup Language）**，  
超文本标记语言（用标签标记普通文本使其表达超出文本范围的内容）。用来编写网页的。  

我们看看[维基百科里http](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)是如何解释的:  

```
超文本传输协议（英语：HyperText Transfer Protocol，缩写：HTTP）  
是一种用于分布式、协作式和超媒体信息系统的应用层协议。  
HTTP是万维网的数据通信的基础。
设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。  
通过HTTP或者HTTPS协议请求的资源由统一资源标识符（Uniform Resource Identifiers，URI）来标识。
```

## 2.版本历史

* 1991年，HTTP/0.9  
只支持GET请求方法获取文本数据（比如HTML文档），且不支持请求头、响应头等，无法向服务器传递太多信息  
* 1996年，HTTP/1.0  
支持POST、HEAD等请求方法，支持请求头、响应头等，支持更多种数据类型（不再局限于文本数据）  
浏览器的每次请求都需要与服务器建立一个TCP连接，请求处理完成后立即断开TCP连接  
* 1997，HTTP/1.1（最经典，使用最广泛的版本）  
支持PUT、DELETE等请求方法  
采用持久连接（Connection keep-alive），多个请求可以共用同一个TCP连接  
* 2015，HTTP/2.0（正在取代1.1）  
* 2018，HTTP/3.0（处于草稿阶段）  

## 3.标准

HTTP的标准是由万维网协会（W3C）、互联网工程任务组（IETF）协调制定，最终发布了一系列的RFC。  
[RFC（Request For Comments）](https://www.rfc-editor.org/standards)，请求意见稿。申请的内容被审核通过后就会成为HTTP标准。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_05.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_05.png?x-oss-process=image/resize,w_800,m_lfit)  

HTTP/1.0最早是在1997年的RFC_2068中记录，该规范在1999年的RFC_2616中已作废，2014年又由RFC_7230系列的RFC取代。  
HTTP/2.0标准于2015年5月以RFC_7540正式发表，取代HTTP/1.1成为HTTP的实现标准  
1996年3月，清华大学提交的适应不同国家和地区中文编码的汉字统一传输标准被IETF通过为RFC_1922，成为中国大陆第一个被认可为RFC文件的提交协议。  

通过抓包本地服务器探索请求过程：

[图片备用地址](https://limingxie.github.io/images/network/application/application_06.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_06.png?x-oss-process=image/resize,w_800,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_07.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_07.png?x-oss-process=image/resize,w_800,m_lfit)  

请求报文和响应报文格式：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_08.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_08.png?x-oss-process=image/resize,w_800,m_lfit)  

HTTP的请求头和响应头格式都是固定的，必须按照指定格式收发数据，否则就无法正常建立通信。  
比例空格、换行、键值对的键（字段名）。  

每个字符（16进制）都有对应的ASCII码值：  
[图片备用地址](https://limingxie.github.io/images/network/application/application_11.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_11.png?x-oss-process=image/resize,w_800,m_lfit)  

为什么使用0d0a呢（既有回车又有换行）？主要是为了兼容不同的操作系统，  
因为有些操作系统0a代表换行，有些操作系统换行的操作符是0d。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
