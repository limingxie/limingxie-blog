---
author: "li_mingxie"
title: "【区块链笔记】BTC_密码学基础(01)"
date: 2022-10-20T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "加密",
    "解密",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`密码学相关的`相关内容。  <!--more-->  
下面几个密码学基础是了解比特币之前必须要掌握的内容，先了解一下。  

比特币是加密货币（crypto-currency）  
区块链上内容都是公开的，包括区块的地址，转账的金额。  
比特币巧妙的利用的hash和签名的密码学技术，保障了隐私和安全。  

## 1.Hash

密码学中用到的哈希函数被称为密码散列函数（cryptographic hash function）  
简单的说把一些内容(有可能很长)，通过一些算法求出一系列的字符串。  
这个字符串有有两种特征。  

1. `hiding(不可逆性)`: 哈希函数的计算过程是单向的，不可逆的。
2. `collision resistance(无碰撞性)`: 哈希碰撞无法人为制造，无法验证，是根据实践经验得来的。

hash算法最常见的地方就是文件签名, 比如我们在一个网站上下载一个安装包，  
想知道下载的过程中有没有被串改，可以算一下hash值和网站上提供的hash值做对比就行了。  

看看hello world的hash值，来源于  
<https://www.fileformat.info/tool/hash.htm>  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_hash.png)  
![blockchain_btc_hash](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_hash.png)  

## 2.对称加密Symmetric-key algorithm

对称加密(Symmetric-key algorithm): 使用相同的秘钥加密和解密。  
就是有个原文，使用秘钥加密，获取密文。  
读取密文时使用相同的秘钥解密获取原文。

```
加密过程： 密钥 + 原文 => 密文
解密过程： 密文 + 密钥 => 原文
```

缺点：无法确保密钥的传输过程是安全的。

## 3.非对称加密

需要两个秘钥：公钥public-key 和 私钥private-key
有以下特征：

1. 公钥一般用于加密，私钥用于解密(私钥加密的内容，使用公钥也可以解开)
2. 公钥由私钥生成，私钥可以推导出公钥，但是公钥不可以推导出私钥。

优点：解决了对称加密的密钥传输的安全问题。  
缺点：加解密过程速度慢。  

所以一般是使用公钥加密 对称加密的密钥 传输。  
然后用私钥解密得出对称加密的 密钥。  
后面使用对称加密的方式传输数据。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
