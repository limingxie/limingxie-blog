---
author: "li_mingxie"
title: "【Oauth2.0笔记】简单整理概念"
date: 2024-09-01T23:28:49+08:00
tags: [
    "Oauth2.0",
]
categories: [
    "Oauth2.0",
]
---

这几天因工作需求查看了Oauth2.0相关的文档，其实之前也是看了好几次了。  
但是过几天就会忘记其原理，岁月不饶人，这记忆...ㅠㅠ  
还是自己做个笔记后续方便查看。<!--more-->

## 1. Oauth2.0的出现

网上资料介绍有很多版本，我最喜欢这种解释。  

有A和B两家互联网企业，现在有个需求，A公司需要访问B公司的用户信息，比如头像，照片之类的。  
这时候技术上如何实现呢？ 

方案一：B公司提供接口
```
GET /photos?account=
```

显然这种方法是无法接受的，因为这样的话，B的用户就没有任何隐私了。

方案二：
```
GET /photos?account=&pwd=
```

貌似还可以，但是凭什么用户把自己B公司的密码存放在A公司呢？  
即使存放在A公司，但是调用B公司接口的时候A公司是很容易获取到用户密码的。  
为了解决这个问题，出现了Auth2.0。

[图片备用地址](https://limingxie.github.io/images/struct/oauth/oauth2_0.png)  
![oauth2_0_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/struct/oauth/oauth2_0.png)

这里我们先看看4个角色：

* 资源所有者：用户就是自己的资源所有者。
* 客户端：A公司的系统是客户端。
* 资源服务器：B公司就是资源服务器(存放头像的服务器)。
* 授权服务器：B公司就是授权服务器(确认用户身份，并给A服务器授权的服务器)。

以下是原版的解释，可以做参考。  
```
* Resource Owner: The resource owner is the user who authorizes an application to access their account.   
  The application’s access to the user’s account is limited to the scope of the authorization granted (e.g. read or write access)
* Client: The client is the application that wants to access the user’s account.   
  Before it may do so, it must be authorized by the user, and the authorization must be validated by the API.
* Resource Server: The resource server hosts the protected user accounts.
* Authorization Server: The authorization server verifies the identity of the user then issues access tokens to the application.
```

## 2. Oauth2.0 授权流程

模拟整个交互过程。  

用户访问A网站，A需要访问B的用户信息。这时A会打开一个页面。  
这个页面就是B部署在鉴权服务器上面的一个鉴权页面，比如A想获取(假设B是腾讯)QQ头像和昵称。  

[图片备用地址](https://limingxie.github.io/images/struct/oauth/oauth2_0_02.png)  
![oauth2_0_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/struct/oauth/oauth2_0_02.png)

这个界面是B提供的，所以所有的信息交互是用户和B在进行，跟A是没关系。  
B的鉴权服务器确认了用户的身份以后，B鉴权服务器会给A发送一个令牌(access token，全局唯一的随机字符串)。  
后续A请求B的时候，都会带上这个令牌，B鉴权服务器会验证这个令牌是否合法。(也可以放到HTTP的Header里面)
```
GET /photos?accesstoken=xxxxx
```

每个access token都有scope，为了限制授权资源的范围。  
每个access token都有过期时间，过期了需要重新申请，被窃取也不能一直用。  


## 3. Access Token

一般Access Token 包含着如下信息：
```
标识符      jti (JWT ID): 用于标识这个 JWT 的唯一标识符。
哪个用户    sub (Subject): 表示这个 JWT 的主题，通常是用户的唯一标识符。
什么时候    iat (Issued At): 时间戳。
过期时间    exp (Expiration Time): 表示这个 JWT 的过期时间戳。
授权范围    scope: 表示这个 JWT 授予的权限范围，这里包含了 openid、profile 和 offline_access。
谁发的      iss (Issuer): 表示这个 JWT 的发行者，通常是认证服务器的 URL。
授权的app   aud (Audience): 表示这个 JWT 的预期接收者，通常是应用的唯一标识符。
```

这里再整理一次获取Access Token的流程：
#### 1.A需要实现一个API接口:
```
GET /recieve_code?code=
```

#### 2.A需要访问B资源的时候，A会打开一个页面，这个页面就是B提供的一个鉴权页面。  
一般是B提供SDK实现这一步的功能。A打开界面你的时候把第一步的接口地址告诉B(或已经存储)。  
当用户执行授权操成功后，平台就会自动调用第一步中提供的 url，并传过来一个code。

[图片备用地址](https://limingxie.github.io/images/struct/oauth/oauth2_0_02.png)  
![oauth2_0_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/struct/oauth/oauth2_0_02.png)

#### 3.第一步接口获取Code后，兑换Access Token。  

这时一般会获取Access Token和refresh token。  
当Access Token到期以后，A可以通过refresh token再次获取新的Access Token。  
这样Access Token过期以后，不用反复地做授权的操作了。

## 4.Refresh Token

为了防止泄露后一直有效，access token是有期限。  
但是有效期，每次Access Token 到期后，重复的让用户给A授权是很麻烦的事情。  
于是 Oauth2.0 引入了 refresh token 机制。  
当鉴权服务器发送access token给使用方时，同时也发送一个 efresh token。   
这个refresh token的有效期很长，作用是可以用来刷新access token。

这里需要注意的是如果refresh token泄露了就很麻烦了。  
所以refresh token一定要保存在使用方的服务器上，而绝不能存放在移动 app、PC端软件、浏览器上，也不能在网络上随便传递。  
调用refresh接口的时候，一定是从使用方服务器到鉴权服务器的https访问。  
所以，refresh token 比 access token 隐蔽得多，也安全得多。

## 5.Authorization Code 授权原理和实现方法

Oauth 2.0有好几种授权流程，最常见的是Authorization Code。其它的可以自行查找看看。  

先看流程图  
[图片备用地址](https://limingxie.github.io/images/struct/oauth/oauth2_0_03.png)  
![oauth2_0_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/struct/oauth/oauth2_0_03.png)

这里解释一下authorization code

* code一个一次性的临时凭证，它可以兑换 access token
* code是由鉴权服务器生成的一个随机且唯一的字符串
* 鉴权服务器会记录下code的生成时间、将要兑换给谁（例子里是 A）、授权的用户是谁
* 在第5步，鉴权服务器会验证A的身份，并且检查code的有效期。通过的话就会生成access token
* code是一次性的，在第5步成功兑换access token之后就会作废
* code的有效期很短，一般不会超过1,2分钟。A在第4步获取到code之后，就立刻走第5步获取Access Token。

#### 为什么不直接获得Access Token而是先返回code？

1. code 的有效期很短，留给攻击者的时间很短
2. code兑换Access Token的时候鉴权服务器还会对A的身份做一次验证。

#### 怎么验证A的身份？

当A和B决定合作，B一般会发给A一个client_id和client_secret。  
A从B那里通过code兑换Access Token的时候，也需要提供client_secret做的签名。  
所以攻击者哪怕劫取了code，也很难获取Access token。除非client_secret泄露了。

## 6.模拟获取过程

#### 1.第一步 用户访问A

```
GET /photos?xxx=?
```
参数应该是根据业务需求来定。

#### 2.第二步 打开授权界面

```
GET /oauth2/auth
```

常用的参数
* client_id => 需要表明调用方。必填
* redirect_uri => 传了做校验不传则使用注册的默认值。选填
* scope => 授权范围。选填
* state => CSRF攻击。A传的值B返回给A。选填

#### 3.第三步 用户授权

这时候B会做以下验证。

* 确认用户身份
* 检查 client_id 是否合法
* 检查 redirect_url 是否和事先注册的相符
* 检查 scope。
* 通过后生成code，并跳转到redirect_url，附上code和state，state是原封不动传回。

#### 4.第四步 A获得code

```
GET /recieve_code?code=
```
这是A要提供的接口。  
在接口里获得code以后，A还需要用client_secret签名，向B请求Access Token。  

#### 5.第五步 A获得Access Token

```
POST /oauth2/token　
```

常用的参数

* code 必填
* client_id 必填
* redirect_uri 选填
* hmac 用client_secret签名 必填

这时B需要做如下验证

* 检查code是否已过期
* 检查code是否已经使用过，如果是使用过，很可能已经泄漏，需要采取相关措施
* 检查client_id是否合法，client_id和code对应的client_id是否一致
* 检查 redirect_uri参数和上面调用Auth Endpoint时传的redirect_uri是否完全一致。
* 检查 hmac
* 检查都通过，废弃掉code，生成access token并返回。
  
#### 6.第六步 A使用Access Token访问资源

```
GET /photos?access_token=xxxx
```

或放到Header中去调用接口，这就看怎么规定调用方式了。

## 7.最后

其实Oauth2.0授权方式有4种，这里只整理了最常用的 授权码 方式。
还有隐藏式，密码式和凭证式。  
这里就没做整理了，后续有机会再整理整理。


----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`





<!-- 

　　1，用户访问 A 网站
　　2，A 打开 B 的授权页面
　　3，用户看到授权界面，输入账号密码进行授权
　　4，B 验证用户是合法的之后，生成一个 authorization code，然后通过 302 跳转的方式，把这个 code 发送给 A
　　5，A 用这个 code 调用 B 的 api，获得 access token
　　6，A 用 access token 访问资源

　　问题来了，authorization code 是什么？
　　- code 一个一次性的临时凭证，可以用它来兑换 access token
　　- code 是由鉴权服务器生成的一个随机且唯一的字符串
　　- 鉴权服务器会记录下 code 的生成时间、将要兑换给谁（例子里是 A）、授权的用户是谁
　　- 在第 5 步，鉴权服务器会验证 A 的身份，并且检查 code 的有效期。通过的话就会生成 access token
　　- code 是一次性的，在第 5 步成功换取 access token 之后就会作废
　　- code 的有效期非常短，可能不会超过 1 分钟。A 在第 4 步获取到 code 之后，要立刻走第 5 步

　　为什么不在第 4 步直接把 access token 发送给 A，而要通过看似多余的 code 再转换一下，搞这么复杂呢？这个问题的答案非常重要，是整个授权流程的关键所在。
　　如果在第 4 步直接把 access token 发送给 A，会有什么问题呢？
　　问题就是，第 4 步是一个 302 跳转，如果在第 4 步传递 access token，会导致 access token 暴露在 url 中，这是非常不安全的方式，因为 url 很容易捕捉到。

　　所以我们不能在第 4 步直接传递 access token。那换成一个 code，再用 code 去换，就更安全了吗？你可以能会问，code 同样会泄漏，那攻击者拿到 code，也可以用 code 去换 access token，有什么区别呢？
　　区别在于，攻击者拿到 code 是不能拿去换 access token 的，两个原因：
　　1，上面说了，换取 access token 的时候，鉴权服务器会验证 A 的身份。具体怎么验证，在后面说明。
　　2，code 的有效期极短，留给攻击者的时间极短
　　另外，即便攻击者成功突破了上面两个限制，拿到了 access token，鉴权服务器也能很快发现。这是因为，攻击者必须抢在 A 之前去兑换 access token。那么，等到 A 走第 5 步的时候，鉴权服务器就会发现，这个 code 已经被用过了。此时这就是一个攻击已经发生的信号：有两个不同的服务器，尝试用同一个 code 来兑换 access token，正常情况下是不应该发生的。此时鉴权服务器可以立刻采取措施。例如，可以把刚才兑换出去的 access token 作废。所以，搞出来一个 code，确实会安全很多。

　　那么，怎么验证 A 的身份呢？

　　所有的 A，应该说都是 B 的合作方。那么在开始合作之前，一定需要做一些前期的准备。具体来说，B 通常会提供一个申请网站，A 通过这个网站填写一些信息，提交合作申请。B 的工作人员会审核，通过之后，系统会给 A 自动分配一个 client_id 和一个 client_secret。client_id 就是一个唯一 id，client_secret 是一个密钥。A 需要把 client_secret 妥善保管在服务器上，不能泄露。在用 code 兑换 access token 的时候，需要用这个 client_secret 对请求生成 MAC（消息验证码）。鉴权服务器就是依靠这个 MAC 来验证 A 的身份的。

　　这里需要用到密码学的知识，本文就不展开解释了，有兴趣请看 什么是消息验证码MAC 。总之，攻击者因为没有这个密钥，无法对请求做出 MAC，因此就算窃取到 code，也不能用 code 来兑换 access token。

　　根据上面的梳理，可以知道，要实现一套 Authorization Code 授权流程，需要做两个 endpoint：

　　1，Auth Endpoint： GET /oauth2/auth　　这就是第 2 步的授权页面地址
　　参数：
　　　　client_id 发起授权的合作方的 client_id。必填
　　　　redirect_uri 回调地址，也就是第 4 步 302 跳转的地址，用于接收 code。这个参数是可选的。因为在前期发起合作申请的时候，A 是需要提前将这个地址注册给 B 的。如果没有传，则使用注册的默认值
　　　　scope 授权范围。这个也是可选的。B 可以把资源进行划分，分成多个 scope。A 发起授权的时候，可以指明要哪些 scope 的访问权限
　　　　state 一个由 A 生成的，随机且唯一的字符串。可选，但强烈建议要有。这个是用来防 CSRF 攻击的，具体请看 Oauth2.0 里面的 state 参数是干什么的

　　这个 endpoint 需要做以下几件事情：
　　　　- 检查 client_id 是否合法
　　　　- 检查 redirect_url 是否和事先注册的相符
　　　　- 检查 scope。如果申请的时候有注册要使用的 scope，这里要检查传入的 scope 是否相符
　　　　- 展示登录框对用户进行验证，展示提示信息，比如“即将授权给 xxx”等
　　　　- 验证用户，通过后生成 code，并 302 跳转到 redirect_url，附上 code 和 state。其中 state 就是参数 state，原封不动传回去即可

　　2，Token Endpoint： POST /oauth2/token　　这就是第 5 步用 code 兑换 access token 的接口
　　参数：
　　　　code 必填
　　　　client_id 必填
　　　　redirect_uri 可选
　　　　hmac 必填

　　这个 endpoint 需要做以下几件事情：
　　　　- 检查 code 是否已过期
　　　　- 检查 code 是否已经使用过，如果是，很可能已经泄漏，需要采取相关措施
　　　　- 检查 client_id 是否合法，client_id 和 code 对应的 client_id 是否一致
　　　　- 检查 redirect_uri 参数和上面调用 Auth Endpoint 时传的 redirect_uri 是否完全一致。为什么这么做，请看 如果攻击者操控了 redirect_uri，会怎样？
　　　　- 检查 hmac
　　　　- 检查都通过，废弃掉 code，生成 access token 并返回

　　以上就是实现最基本、最核心的 authorization code 授权流程需要做的事情。但实际中，这样很有可能是不够的。主要是因为 access token 是有有效期的。过期之后，会有两个很常见的需求：
　　a，刷新 access token。具体请看 这篇文章
　　b，静默授权。如果 A 方没有 refresh token，或者 refresh token 过期了，但又不想让用户重新进行授权操作，希望可以在无需用户参与的情况下获得新的 access token

　　为了满足需求 a，需要给 Token Endpoint 增加两个参数：grant_type 和 refresh_token，如下：

　　Token Endpoint： POST /oauth2/token　
　　参数：
　　　　grant_type 必填。如果是用 code 兑换 access token，传 authorization_code；如果是用 refresh token 刷新，传 refresh_token
　　　　code 当 grant_type = authorization_code 时必填
　　　　client_id 必填
　　　　redirect_uri 可选
　　　　refresh_token 当 grant_type = refresh_token 时必填
　　　　hmac 必填

　　为了满足需求 b，需要给 Auth Endpoint 增加一个参数： prompt，如下：

　　Auth Endpoint： GET /oauth2/auth
　　参数：
　　　　client_id 必填
　　　　redirect_uri 可选
　　　　scope 可选
　　　　prompt 可选。可传 none 或不传。如果不传，则按正常展示授权页面，如果传 prompt = none，则不展示页面，改为检查该用户最近是否执行过相同的授权，如果授权过，这次则直接自动执行授权而无需用户参与，也就是静默授权。但是如果不符合条件，报错
　　　　state 可选

　　实现了这两个 api，基本上就实现了基本的 Authorization Code 授权流程。
 -->