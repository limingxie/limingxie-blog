---
author: "li_mingxie"
title: "数字证书(Digital Certificate)简介"
date: 2022-04-11T22:28:49+08:00
tags: [
    "digital",
    "certificate",
]
categories: [
    "network",
]
---

## 1.数字证书是什么？

数字证书(digital certificate)是互联网通讯中标志通讯各方身份信息的一系列数据，提供了验证身份的方式。  
它是由一个由权威机构（Certificate Authority）中心发行的，人们可以在网上用它来识别对方的身份。  
最简单的数字证书包含一个公开密钥、名称以及证书授权中心的数字签名。  

## 2.常见的3种加密方式

为了了解数字证书的原理，首先需要了解已下3种加密方式。

#### 2.1 hash算法加密

它是一种不可逆的加密方式，对一组数据使用哈希算法加密，加密后不能解密。  
比如用户的密码保存的时候，先hash方法加密后保存。验证时，输入的密码也加密后比较hash值就可以了。  
这样的好处是即使用户信息泄露了也没关系，能看到的都是加密后的hash值。  

#### 2.2 对称密钥加密

加密一组数据时使用【秘钥】加密。  
解密时候也使用同样的【秘钥】解密。  

#### 2.3 非对称密钥加密

有2个秘钥分别是，【公钥】和【私钥】。  
使用【公钥】加密的数据只能用【私钥】解密。  
使用【私钥】加密的数据只能用【公钥】解密。

这3种加密方式如果不太理解的话，可以自行在搜一搜相关资料看看。  
下面我们看看数据证书是如何发放的。

## 3.发放数字证书

看看下图CA是如何发放数字证书。

[图片备用地址](https://limingxie.github.io/images/network/digital_certificate/digital_certificate_2.png)  
![digital_certificate](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/digital_certificate/digital_certificate_2.png)

```
1. 创建一个文件包含申请者信息(申请者的公钥，签名算法，有效期等等...)的文件。
2. 对这个文件使用hash算法加密，获取hash值。
3. CA使用自己的私钥加密申请信息的hash值获取证书签名。
4. 证书签名+申请者信息 = 数字证书
```

## 4.验证数字证书的合法性

看下图如何判断数字证书的合法性。

[图片备用地址](https://limingxie.github.io/images/network/digital_certificate/digital_certificate_3.png)  
![digital_certificate](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/digital_certificate/digital_certificate_3.png)

```
1. 先把数字证书里的申请者信息使用hash算法加密获得hash值。
2. 使用CA颁发的公钥解密数字证书里的签名。
3. 比较数字证书里的内容获得的hash值和解密签名获取的hash值是否一样。
```

## 5.数据证书里的详细内容

最后整理了数字证书的详细内容和说明。

[图片备用地址](https://limingxie.github.io/images/network/digital_certificate/digital_certificate.png)  
![digital_certificate](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/network/digital_certificate/digital_certificate.png?x-oss-process=image/resize,w_800,m_lfit)

|||
|-|-|
|**版本:**|指定所编码证书的版本号。 当前，此字段的可能值为 0、1 或 2。|  
|**序列号:**|包含证书颁发机构 (CA) 分配给证书的一个唯一正整数。|
|**签名算法:**|包含一个对象标识符 (OID)，指定 CA 用于对证书进行签名的算法。|
|**签名哈希算法:**|生成签名使用的哈希算法。|
|**颁发者:**|包含创建和签名证书的 CA 的 X.500 可分辨名称 (DN)。|
|**有效期:**|指定证书有效的时间间隔。|
|**使用者:**|包含实体的 X.500 可分辨名称，该实体与证书中包含的公钥相关联。|
|**公钥:**|包含公钥和关联的算法信息。|
|**授权密钥标识符:**|标识证书颁发机构 (CA) 公钥，与用于签署证书的 CA 私钥对应。|
|**基本约束:**|指定实体是否可用作 CA，如果可以，则指定在证书链中该 CA 下可以存在的从属 CA 的数量。|
|**证书策略:**|指定颁发证书的策略和使用证书的目的。|
|**CRL:**|分发点	包含基本证书吊销列表 (CRL) 的 URI。|
|**增强型密钥用法:**|指定证书中包含的公钥的使用方式。|
|**颁发者备用名称:**|为证书请求颁发者指定一个或多个备用名称形式。|
|**密钥用法:**|指定证书中包含的公钥可以执行的操作的限制。|
|**名称约束:**|指定证书层次结构中所有使用者名称必须位于的命名空间。 扩展仅在 CA 证书中使用。|
|**策略约束:**|通过禁止策略映射或通过要求层次结构中的每个证书包含一个可接受的策略标识符来约束路径验证。 扩展仅在 CA 证书中使用。|
|**策略映射:**|指定与发证 CA 中的策略对应的从属 CA 中的策略。|
|**私钥使用周期:**|为私钥指定与私钥关联的证书不同的验证周期。|
|**使用者可选名称:**|为证书请求使用者指定一个或多个备用名称形式。 示例备用形式包括电子邮件地址、DNS 名称、IP 地址和 URI。|
|**使用目录属性:**|传达标识属性，如证书使用者的国籍。 扩展值是 OID 值对序列。|
|**使用者密钥标识符:**|区分证书使用者持有的多个公钥。 扩展值一般是密钥的 SHA-1 哈希。|


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`