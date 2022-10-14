---
author: "li_mingxie"
title: "【nginx笔记】nginx_配置策略(02)"
date: 2022-10-17T23:28:49+08:00
tags: [
    "nginx",
]
categories: [
    "nginx",
]
---

这一片简单的整理了 **`nginx_配置策略`** 相关的内容。<!--more-->  

## 1.servername匹配规则

#### 完整匹配

我们可以在同一servername中匹配多个域名

```
server_name  vod.mmban.com www1.mmban.com;
```

#### 通配符匹配

```
server_name  *.mmban.com
```

#### 通配符结束匹配

```
server_name  vod.*;
```

#### 正则匹配

```
server_name  ~^[0-9]+\.mmban\.com$;
```

## 2.反向代理和正向代理

总是说反向代理，起初不太明白。其实很简单。  
传统的代理服务器是:

```
 --------------------
| 客户端  -> 代理服务器 | -> 目标服务器
 --------------------
```

> 其中客户端和代理服务器是一伙的，他两合作链目标服务器。

反向代理是客户端链接代理服务器，代理服务器帮助服务器判断，链那个业务服务器。

```
          -----------------------
客户端 -> | 代理服务器 -> 目标服务器 |
          -----------------------
```

> 反向代理是客户端只关心链服务，不关系服务器是如何处理。

[图片备用地址](https://limingxie.github.io/images/system/nginx/nginx_01.png)  
![go_nginx_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/nginx/nginx_01.png)

## 3.负载均衡

### 3.1 具体配置

```
upstream httpd {
    server 192.168.0.102:80;
    server 192.168.0.103:80;
}
```

### 3.2 负载均衡策略

#### 轮询

默认情况下使用轮询方式，逐一转发，这种方式适用于无状态请求。

#### weight(权重)

指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。

```
upstream httpd {
    server 127.0.0.1:8050   weight=6 down;
    server 127.0.0.1:8060   weight=2;
    server 127.0.0.1:8060   weight=2 backup;
}
```

* `down`:表示当前的server暂时不参与负载。
* `weight`:默认为1.weight越大，负载的权重就越大。
* `backup`: 其它所有的非backup机器down或者忙的时候，请求backup机器。

以下命令几乎不怎么用，了解即可  

* **ip_hash**: 根据客户端的ip地址转发同一台服务器，可以保持回话。
* **least_conn**: 最少连接访问
* **url_hash**: 根据用户访问的url定向转发请求
* **fair**: 根据后端服务器响应时间转发请求

## 3.动静分离

就是把动态资源和静态资源分离出来，让nginx直接访问静态资源，  
不需要再访问服务器，转接给客户端。

```
location / {
    proxy_pass http://127.0.0.1:8080;
    root   html;
    index  index.html index.htm;
}
```

==> 把静态文件放到领路径下访问，不用在访问实际业务服务器。

```
location /css {
    root   /usr/local/nginx/static;
    index  index.html index.htm;
}
location /images {
    root   /usr/local/nginx/static;
    index  index.html index.htm;
}
location /js {
    root   /usr/local/nginx/static;
    index  index.html index.htm;
}
```

也可以使用正则

```
location ~*/(css|img|js) {
    root   /usr/local/nginx/static;
    index  index.html index.htm;
}
```

## 4.UrlRewrite

rewrite语法格式及参数语法

```
rewrite是实现URL重写的关键指令，根据regex (正则表达式)部分内容，  
重定向到replacement，结尾是flag标记。  

rewrite     <regex>     <replacement>   [flag];  
 关键字        正则         替代内容       flag标记  

关键字:其中关键字error_log不能改变  

正则:perl兼容正则表达式语句进行规则匹配  
替代内容:将正则匹配的内容替换成replacement  
flag标记:rewrite支持的flag标记

rewrite参数的标签段位置:  
server,location,if

flag标记说明:
last #本条规则匹配完成后，继续向下匹配新的location URI规则  
break #本条规则匹配完成即终止，不再匹配后面的任何规则
redirect #返回302临时重定向，浏览器地址会显示跳转后的URL地址  
permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址
```

实例

```
rewrite   ^/([0-9]+).html$     /index.jsp?pageNum=$1    break;
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
