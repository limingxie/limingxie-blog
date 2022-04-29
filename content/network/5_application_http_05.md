---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTP协议简介(5)"
date: 2022-05-01T18:05:49+08:00
tags: [
    "OSI",
    "CORS",
    "cookie",
    "session",
    "network",
]
categories: [
    "network",
]
---

这一篇整理了跨域，cookie和session的基本概念和用法。  

## 1.跨域(Cross-origin resource sharing）

说起跨域我们需要先了解同源策略。  

### 1.1 同源策略(Same-Origin Policy)

我们看看[维基百科](https://zh.wikipedia.org/wiki/%E5%90%8C%E6%BA%90%E7%AD%96%E7%95%A5)是如何解释的:  

同源策略是指在Web浏览器中，允许某个网页脚本访问另一个网页的数据，  
但前提是这两个网页必须**有相同的URI、主机名和端口号**(协议protocol, 主机host, 端口号port)，一旦两个网站满足上述条件，这两个网站就被认定为具有相同来源。  
此策略可防止某个网页上的恶意脚本通过该页面的文档对象模型访问另一网页上的敏感数据。  

同源策略对Web应用程序具有特殊意义，因为Web应用程序广泛依赖于HTTP cookie来维持用户会话，所以必须将不相关网站严格分隔，以防止丢失数据泄露。  
值得注意的是同源策略仅适用于脚本，这意味着某网站可以通过相应的HTML标签访问不同来源网站上的图像、CSS和动态加载脚本等资源。  
而跨站请求伪造就是利用同源策略不适用于HTML标签的缺陷。  

> a、img、script、link、iframe、video、audio等标签不受同源策略的约束。  

下面是维基百科中举得例子。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_21.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_21.png?x-oss-process=image/resize,w_700,m_lfit)  

### 1.2 为什么会出现跨域问题

出于浏览器的同源策略限制。  
举例子前端部署在服务器A，后端项目部署在服务器B，访问服务器A的前端页面，页面上点击按钮会访问服务器B的资源，这时候就会发生跨域。  

> 注意：跨域发送的请求，服务端是能够正常响应的(结果都返回到游览器)，只是数据返回到浏览器的时候被浏览器劫持了。  

### 1.3 跨域解决方法

**CORS跨域资源分享**(Cross-Origin Resource Sharing)的缩写。它是W3C标准，属于跨源AJAX请求的根本解决方法。  

解决办法有以下两种：  

1. 普通跨域请求：只需服务器端设置响应头Access-Control-Allow-Origin，告知浏览器这是一个允许跨域访问的请求。  
虽然CORS的实现需要客户端和服务端同时支持，但现在的浏览器基本都支持（IE至少是IE10版本）。  
2. 带cookie跨域请求：前后端都需要进行设置  

简单的看看下面java代码：  

```java
// 允许跨域访问的域名：若有端口需写全（协议+域名+端口），若没有端口末尾不用加'/'
response.setHeader("Access-Control-Allow-Origin", "http://www.example.com");

// 允许前端带认证cookie：启用此项后，上面的域名不能为'*'，必须指定具体的域名，否则浏览器会提示
response.setHeader("Access-Control-Allow-Credentials", "true");

// 提示OPTIONS预检时，后端需要设置的两个常用自定义头
response.setHeader("Access-Control-Allow-Headers", "Content-Type,X-Requested-With");
```

## 2.Cookie

因为HTTP协议是无状态的，即服务器不知道用户上一次做了什么，这严重阻碍了交互式Web应用程序的实现。  
典型的应用场景是当用户登录网站以后，发出下一个请求时，服务器是不知道他之前登录过。  
这时使用Cookie就能解决当前的问题(现在也流行附加令牌token的方式)。  
当用户登录成功以后，服务器发送了包含登录凭据（用户名加密码的某种加密形式）的Cookie到用户的硬盘上。  
下一次发请求时，客户端把Cookie附加到请求头，  
服务器可以读取Cookie中包含的信息，借此可以判断用户是否登录。  

### 2.1 分类

Cookie保存在客户端中，按在客户端中的存储位置，可分为**内存Cookie**和**硬盘Cookie**。  

内存 Cookie 由浏览器维护，保存在内存中，浏览器关闭即消失，存在时间短暂。  
硬盘Cookie保存在硬盘里，有过期时间，除非用户手动清理或到了过期时间，硬盘Cookie不会清除，存在时间较长。  
所以，按存在时间，可分为**非持久Cookie**和**持久Cookie**。  

### 2.2 不可跨域名行为

很多网站都会使用Cookie。例如，Google会向客户端颁发Cookie，Baidu也会向客户端颁发Cookie。  
那游览器是如何判断Cookies和网站之间的关系? 是用域名判断。

Cookie具有不可跨域名性。根据Cookie规范，浏览器访问Google只会携带Google的Cookie。  
Cookie在客户端是由浏览器来管理。浏览器能够保证Google只会操作Google的Cookie而不会操作其它域名的Cookie，从而保证用户的隐私安全。  

正常情况下，同一个一级域名下的两个二级域名如www.google.com和images.google.com也不能交互使用Cookie，因为二者的域名并不严格相同。  
如果想所有google.com名下的二级域名都可以使用该Cookie，需要设置Cookie的domain参数。  

```html
Cookie cookie = new Cookie("time","20200000"); // 新建Cookie  
cookie.setDomain(".google.com"); // 设置域名  
cookie.setPath("/"); // 设置路径  
cookie.setMaxAge(Integer.MAX_VALUE); // 设置有效期  
response.addCookie(cookie); // 输出到客户端  
```

### 2.3 常用属性

除了name与value之外，Cookie还具有其他几个常用的属性。  
看看下图：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_22.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_22.png)  

### 2.4 有效期

Cookie的maxAge决定着Cookie的有效期，单位为秒（Second）。Cookie中通过getMaxAge() 方法与setMaxAge(int maxAge)。  

```html
Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie  
cookie.setMaxAge(Integer.MAX_VALUE); // 设置生命周期为MAX_VALUE  
response.addCookie(cookie); // 输出到客户端  
```

### 2.5 修改/删除

Cookie并不提供修改、删除操作。如果要删除某个Cookie，只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。  
注意是0而不是负数。负数代表其他的意义。  

注意：修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。  
否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。  

### 2.6 路径

标识指定了主机下的哪些路径可以接受Cookie，子路径也会被匹配。  

例如，如果只允许/hello/  
下的程序使用Cookie，可以这么写：  

```html
cookie.setPath("/hello/"); // 设置路径  
```

设置为“/”时允许所有路径使用Cookie。path属性需要使用符号“/”结尾。name相同但domain相同的两个Cookie也是两个不同的Cookie。  
注意：页面只能获取它属于的Path的Cookie。例如/hello/test/a.html不能获取到路径为/hello/abc/的Cookie。  

### 2.7 安全

HTTP协议不仅是无状态的，而且是不安全的。  
使用HTTP协议的数据不经过任何加密就直接在网络上传播，有被截获的可能。使用HTTP协议传输很机密的内容是一种隐患。  
如果不希望Cookie在HTTP等非安全协议中传输，可以设置Cookie的secure属性为true。  
浏览器只会在HTTPS和SSL等安全协议中传输此类Cookie。

```html
cookie.setSecure(true);
```

secure属性并不能对Cookie内容加密，因而不能保证绝对的安全性。  
如果需要高安全性，需要在程序中对Cookie内容加密、解密，以防泄密。

## 3.Session

### 3.1 概念

**会话(session，Microsoft Windows 中文版译作工作阶段)**是一种持久网络协议，  
在用户（或用户代理）端和服务器端之间创建关联，从而起到交换数据包的作用机制，session在网络协议（例如telnet或FTP）中是非常重要的部分。

在不包含会话层（例如UDP）或者是无法长时间驻留会话层（例如HTTP）的传输协议中，会话的维持需要依靠在传输数据中的高级别程序。  
例如，在浏览器和远程主机之间的HTTP传输中，HTTP cookie就会被用来包含一些相关的信息，例如session ID，参数和权限信息等。

### 3.2 生命周期和有效期

Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。  
每个用户都会有一个独立的Session，而且Session里的信息应该尽量精简。  

session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。  
用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

由于会有越来越多的用户访问服务器，因此Session也会越来越多。  
为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。  
这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。

### 3.3 常用方法

[图片备用地址](https://limingxie.github.io/images/network/application/application_24.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_24.png)  

### 3.4 浏览器的要求

虽然Session保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的支持。这是因为Session需要使用Cookie作为识别标志。  
HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一客户，  
因此服务器向客户端浏览器发送一个Cookie，它的值为该Session的id（也就是HttpSession.getId()的返回值）。  
Session依据该Cookie来识别是否为同一用户。(**同一个游览器可以相互识别，不同游览器就不能相互识别。**)  

该Cookie为服务器自动生成的，它的maxAge属性一般为–1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。  

绝大多数的手机浏览器都不支持Cookie。Java Web提供了另一种解决方案：URL地址重写。  

## 3.5 URL地址重写

URL地址重写是对客户端不支持Cookie的解决方案。URL地址重写的原理是将该用户Session的id信息重写到URL地址中。  
服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。

## 4.cookie和session的总结

我们先看看一个登录的简单流程:
[图片备用地址](https://limingxie.github.io/images/network/application/application_23.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_23.png?x-oss-process=image/resize,w_700,m_lfit)  

1. cookie数据存放在客户的浏览器上，session数据放在服务器上。  
Session信息是存放在server端，但session id是存放在client cookie的。Cookie是完全保持在客户端的。

2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗考虑到安全应当使用session。  
虽然cookie不安全，但是可以加密。

3. 设置cookie时间可以使cookie过期。但是使用session-destory ()，我们将会销毁会话。

4. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用cookie。

5. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie(Session对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)。

6. session很容易失效,用户体验很差;

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
