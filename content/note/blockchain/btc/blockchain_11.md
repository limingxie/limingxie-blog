---
author: "li_mingxie"
title: "【区块链笔记】BTC_最后(11)"
date: 2022-10-30T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "匿名性",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`比特币的最后几个话题`相关的内容。  <!--more-->  
  
## 1.哈希指针

BTC系统中很多地方使用到了哈希指针。指针保存的本地内存地址，只有在本地计算机上才具有意义，如果发送给其他计算机就没有意义了。  
那么区块发布的时候是如何知道上一个区块的信息呢？  
其实哈希指针只是形象化的说法，实际区块Header里保存的是前一个hash值。  
可以参考下面的源代码结构。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/block_header_struct.png)  
![blockchain_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/block_header_struct.png)  

全节点一般将区块存储于一个key-value数据库中，key为哈希，value为区块内容。  
常用的key-value数据库为levelDB，只要知道最后一个区块的哈希值，可以通过levelDB查找哈希值key对应的value区块的内容取出来。  
区块的块头里又有指向前一个区块的哈希值，这样一步步往前可以查询到整个区块所有信息。  

## 2.私钥分段保存

几个人一起分段保存私钥，有很大的安全隐患。因为这样会大大的降低破解难度(2^256和2^128不是一个量级别)。  
如果4人的分1/4的私钥，那很有可能3个人合伙把剩下的1/4破解后私吞。  
这时最好使用多重签名的方式。  

## 3.分布式共识

理论上说异步的分布式系统是不可能达成共识。  
那比特币是如何实现的？  

严格来说，BTC并没有取得真正意义的共识，  
BTC系统取得的共识随时可能被推翻，例如分叉攻击导致系统回滚。  
但是实际操作起来，又感觉实现了分布式共识。  

现实中理论和实际是需要相互结合共存的。  
举例子，当你连接不到远程服务器的时候，你是无法确认是网络堵塞还是服务器死机了。  
但是你可以给那边的人`打电话`确认一下，如果没死机就是网络堵塞，死机了就帮你重启服务器。  

> 不要被理论知识限制我们的思维方式，以及实际的实现。

## 4.BTC的稀缺性

前期为了宣传BTC的挖矿奖励很丰富，但是时间的推移比特币的总量是有限的(2100万)。  
这样导致比特币会越来越稀缺，满足不了增长的财富需求。  
这个性质是其实不适合做货币。一般的货币都有适当的通胀的功能，这有利于经济发展。  
黄金也是增加的产量满足不了财富增长的速度，后续不做金本位了。

## 5.量子计算与区块链

比特币是建立在密码学上的加密货币，在量子计算出来后会不会变得不安全。  

1. 现实中如果真的使用量子计算破解密码的话，最先受威胁的是传统的金融行业。  
    比起他们比特币的市值就很微小了。  
2. 当量子计算出现这些密码学和加密方式也会有相对应的发展更新。
3. 还有hash的算法是不可逆的一种算法是无法推到出公钥和私钥的。  
    因为很长的一连串信息缩短到几百长度的内容，所以对内容是有丢失的。不像加密解密，不丢失数据。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`