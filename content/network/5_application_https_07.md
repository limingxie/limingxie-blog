---
author: "li_mingxie"
title: "【网络协议笔记】第五层:应用层(Application)HTTPS协议简介(7)"
date: 2022-04-24T18:05:49+08:00
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

这一篇整理了对称加密，非对称加密，数字签名，证书。  
<!--more-->
[图片备用地址](https://limingxie.github.io/images/network/application/application_51.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_51.png?x-oss-process=image/resize,w_700,m_lfit)  

## 1.对称加密

在对称加密中（也叫对称密码），加密和解密使用的是**同一个密钥**。

[图片备用地址](https://limingxie.github.io/images/network/application/application_52.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_52.png?x-oss-process=image/resize,w_700,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/network/application/application_53.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_53.png?x-oss-process=image/resize,w_700,m_lfit)  

常见的对称加密算法：  

* DES  
* 3DES  
* AES  

### 1.1 DES（Data Encryption Standard）

[图片备用地址](https://limingxie.github.io/images/network/application/application_54.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_54.png?x-oss-process=image/resize,w_700,m_lfit)  

DES是一种将64bit明文加密成64bit密文的对称加密算法，密钥长度是56bit。  

规格上来说，密钥长度是64bit，但每隔7bit会设置一个用于错误检查的bit，因此密钥长度实质上是56bit。  
由于DES每次只能加密64bit的数据，遇到比较大的数据，需要对DES加密进行迭代（反复）。  
目前已经可以在短时间内被破解，所以不建议使用。  

### 1.2 3DES（Triple Data Encryption Algorithm）

3DES，是将DES重复3次所得到的的一种密码算法，也叫做3重DES。  

三重DES并不是进行三次DES加密(**加密 -> 加密 -> 加密**)，而是**加密 -> 解密 -> 加密**的过程。  
目前还被一些银行等机构使用，但处理速度不高，安全性逐渐暴露出问题。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_55.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_55.png?x-oss-process=image/resize,w_700,m_lfit)  

3个密钥都是不同的，也称为DES-EDE3。EDE是Encryption Decryption Encryption的缩写。  
如果所有密钥都使用同一个，则结果与普通的DES是等价的。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_56.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_56.png?x-oss-process=image/resize,w_700,m_lfit)  

如果密钥1、密钥3相同，密钥2不同，称为DES-EDE2。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_57.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_57.png?x-oss-process=image/resize,w_700,m_lfit)  

### 1.3 AES（Advanced Encryption Standard）

AES是取代DES成为新标准的一种对称加密算法，又称为Rijndael加密法（算法发明人叫做Rijndael）。  
AES的密钥长度有128、192、256bit三种。目前AES已经逐步取代DES、3DES，成为首选的对称加密算法。  
一般来说，我们也不应该去使用任何自制的密码算法，而是应该使用AES。因为它经过了全世界密码学家所进行的高品质验证工作。  

### 1.4 密钥配送问题

在使用对称加密时，一定会遇到密钥配送问题。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_58.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_58.png?x-oss-process=image/resize,w_700,m_lfit)

如果Alice将使用对称加密过的消息发给了Bob，只有将密钥发送给Bob，Bob才能完成解密。  
在发送密钥过程中，可能会被Eve窃取密钥，最后Eve也能完成解密。  

如何解决密钥配送问题？  
有以下几种解决密钥配送的方法：  

* 事先共享密钥（比如私下共享）  
* 密钥分配中心（Key Distribution Center，简称KDC）  
* Diffie-Hellman密钥交换  
* 非对称加密  

## 2.非对称加密

在**非对称加密**(Asymmetric Cryptography)中，密钥分为加密密钥、解密密钥2种，它们并不是同一个密钥。  

**加密密钥:** 一般是公开的，因此该密钥称为公钥（public key）。因此，非对称加密也被称为公钥密码（Public-key Cryptography）。  
**解密密钥:** 由消息接收者自己保管的，不能公开，因此也称为私钥（private key）。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_59.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_59.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/application/application_60.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_60.png?x-oss-process=image/resize,w_700,m_lfit)

### 2.1 公钥和私钥

公钥和私钥是一一对应的，不能单独生成。一对公钥和私钥统称为密钥对（key pair）。  
由公钥加密的密文，必须使用与该公钥对应的私钥才能解密。  
由私钥加密的密文，必须使用与该私钥对应的公钥才能解密。  
因为非对称加密算法更加复杂（安全），所以加密解密速度会比较慢（效率低）。相反而言，对称加密简单（不安全），所以加密解密速度更快（效率高）。  

### 2.2 解决密钥配送问题

由消息的接收者生成一对公钥和私钥。将公钥发给消息的发送者，消息的发送者使用公钥加密消息。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_61.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_61.png?x-oss-process=image/resize,w_700,m_lfit)

### 2.3 RSA

RSA的名字由它的3位开发者姓氏首字母组成（Rivest、Adi Shamir、Leonard Adleman），也是目前使用最广泛的非对称加密算法。  

### 2.4 混合密码系统（Hybrid Cryptosystem）

**对称加密的缺点:** 不能很好地解决密钥配送问题（密钥会被窃取）。  
**非对称加密的缺点:** 加密解密速度比较慢。  
**混合密码系统:** 是将对称加密和非对称加密的优势相结合的方法。**解决了非对称加密速度慢的问题**，并通过非对称加密解决了对称加密的密钥配送问题。  

网络上的密码通信所用的SSL/TLS都运用了混合密码系统。  

#### 2.4.1 混合密码-加密

会话密钥（session key）：  

* 为本次通信随机生成的临时密钥  
* 作为对称加密的密钥，用于加密消息，提高速度  

加密步骤（发送消息）：  

* 消息发送者要拥有消息接收者的公钥  
* 生成会话密钥，作为对称加密的密钥，加密消息  
* 用消息接收者的公钥，加密会话密钥  
* 将前2步生成的加密结果，一并发给消息接收者  

发送出去的内容包括：  

* 用会话密钥加密的消息（加密方法：对称加密）  
* 用公钥加密的会话密钥（加密方法：非对称加密）  

[图片备用地址](https://limingxie.github.io/images/network/application/application_62.jpeg)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_62.jpeg?x-oss-process=image/resize,w_700,m_lfit)

#### 2.4.2 混合密码-解密

解密步骤（收到消息）：  

* 消息接收者用自己的私钥解密出会话密钥  
* 再用第一步解密出来的会话密钥解密消息  

[图片备用地址](https://limingxie.github.io/images/network/application/application_63.jpeg)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_63.jpeg?x-oss-process=image/resize,w_700,m_lfit)

#### 2.4.3 混合密码-加密解密流程

场景：Alice发送消息给Bob。  

发送过程（加密过程）：  

* Bob先生成一对公钥和私钥  
* Bob把公钥共享给Alice  
* Alice随机生成一个会话密钥（临时密钥）  
* Alice用会话密钥加密需要发送的消息（使用的是对称加密）  
* Alice用Bob的公钥加密会话密钥（使用的是非对称加密）  
* Alice把第4、5步的加密结果一并发送给Bob  

接收过程（解密过程）：  

* Bob利用自己的私钥解密会话密钥（使用的是非对称加密算法进行解密）  
* Bob利用会话密钥解密发送过来的消息（使用的是对称加密算法进行解密）  

## 3.数字签名

例如，Alice发送内容给Bob（明文）。Alice发的内容有可能是被篡改的，或者有人伪装成Alice发消息，或者就是Alice发的，但她可以否认。  

问题来了：Bob如何确定这段消息的真实性？如何识别篡改、伪装、否认？  
解决方案：数字签名。  

### 3.1 数字签名过程

在数字签名技术中，有以下2种行为：  

* 生成签名  
    由消息的发送者完成，通过“签名密钥”生成  
* 验证签名  
    由消息的接收者完成，通过“验证密钥”验证  

如何能保证这个签名是消息发送者自己签的？用消息发送者的私钥进行签名。  

签名过程：

[图片备用地址](https://limingxie.github.io/images/network/application/application_64.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_64.png?x-oss-process=image/resize,w_700,m_lfit)

由于非对称加密速度比较慢，当消息体量比较大时效率就会下降，  
所以可以利用单向散列函数对其过程进行改进（对散列值进行加密解密，然后比较散列值）：  

[图片备用地址](https://limingxie.github.io/images/network/application/application_65.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_65.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/network/application/application_66.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_66.png?x-oss-process=image/resize,w_700,m_lfit)

**消息发送者生成密钥对是为了防止密钥被篡改。消息接收者生成密钥对是不想消息被其他人看到。**  

* 如果有人篡改了消息内容或签名内容，会是什么结果？  
    签名验证失败，证明内容被篡改了。  

* 数字签名不能保证机密性？  
    数字签名的作用不是为了保证机密性，仅仅是为了能够识别内容有没有被篡改。  

* 数字签名的作用  
    确认消息的完整性  
    识别消息是否被篡改  
    防止消息发送人否认  

### 3.2 非对称加密和数字签名对比

在非对称加密中，任何人都可以使用公钥进行加密。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_67.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_67.png?x-oss-process=image/resize,w_700,m_lfit)

在数字签名中，任何人都可以使用公钥验证签名。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_68.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_68.png?x-oss-process=image/resize,w_700,m_lfit)

数字签名，其实就是将非对称加密反过来使用。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_69.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_69.png?x-oss-process=image/resize,w_700,m_lfit)

**非对称加密:** 既然是加密，那肯定是不希望别人知道我的消息，所以只有我才能解密。公钥负责加密，私钥负责解密。  
**数字签名:** 既然是签名，肯定是不希望有人冒充我发消息，所以只有我才能签名。 私钥负责签名，公钥负责验签。  

### 3.3 公钥的合法性

如果遭遇了中间人攻击，那么公钥将可能是伪造的。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_70.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_70.png?x-oss-process=image/resize,w_700,m_lfit)

对称加密无法保证密钥的安全，后面有了非对称加密。  
但是非对称加密也有公钥被窃取的风险（混合加密和签名也是一样的，只要有传输公钥这一步骤，就会有窃取风险），  
所以如何才能验证公钥的合法性呢？使用证书。  

## 4.证书

说到证书，首先联想到的是驾驶证、毕业证、英语四六级证等，这些都是由权威机构认证的。  

密码学中的证书，全程叫**公钥证书**(**ublic-key Certificate，PKC**)，跟驾驶证类似，里面有姓名、邮箱等个人信息，以及此人的公钥。  
并由认证机构(**Certificate Authority，CA**)进行数字签名。  

CA就是能够认定“公钥确实属于此人”并能够生成数字签名的个人或者组织。  
CA可以是有国际性的组织、政府设立的组织、通过提供认证服务来盈利的企业，个人也可以成立认证机构。  

### 4.1 注册和下载

[图片备用地址](https://limingxie.github.io/images/network/application/application_71.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_71.png?x-oss-process=image/resize,w_700,m_lfit)

### 4.2 证书的使用

[图片备用地址](https://limingxie.github.io/images/network/application/application_72.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_72.png?x-oss-process=image/resize,w_700,m_lfit)

第4步和第5步只要被篡改，就会认证失败。因为证书签名始终都是围绕CA认证机构验证的。  
各大CA的公钥，默认已经内置在浏览器和操作系统中。  

### 4.3 查看证书

#### 4.3.1 Windows

1. Windows + R -> 输入mmc  
2. 文件 -> 添加/删除管理单元  
3. 证书 -> 添加 -> 我的用户账户 -> 完成 -> 确定  

[图片备用地址](https://limingxie.github.io/images/network/application/application_73.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_73.png?x-oss-process=image/resize,w_700,m_lfit)

#### 4.3.2 Mac

Mac系统的证书统一在**钥匙串**中管理。  

[图片备用地址](https://limingxie.github.io/images/network/application/application_74.png)  
![application](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/application/application_74.png?x-oss-process=image/resize,w_700,m_lfit)  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
