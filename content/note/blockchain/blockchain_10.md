---
author: "li_mingxie"
title: "【区块链笔记】BTC_匿名性(10)"
date: 2022-10-29T23:28:49+08:00
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

这篇文章简单的整理了`比特币的匿名性`相关的内容。  <!--more-->  
  
## 1.比特币的实际匿名性

比特币里的匿名其实是个化名，比现金差。(现金是真正保障了匿名性。)  

如果银行可以用化名匿名性比比特币更好，因为比特币账本是公开的。

    InPuts:  addr1 (4BTC)  addr2 (5BTC)  
    Outputs: addr3 (6BTC)  addr4 (3BTC)  

看这笔信息很容易知道这两个输入有可能是一个人，  
而且2个账户中钱多的是付款账户，钱少的肯定是找零的地址。  

1. 虽然你可以创建很多地址账户，但是看交易是可以关联起来
2. 这些地址账户也很可能跟你的现实身份关键起来。(比如比特币换现金，或比特币网购。)

所以比特币的实际匿名性不是想象中那么好。  

## 2.如何提高比特币的匿名性

在一般生活中想要保持自己的匿名性，比特币是充分满足的。  
但是做一些违法的事情，比特币的公开账户和跟现实交互的时候，很容易暴露你的身份。  

从应用层看: 可以将各个不同用户的BTC混合在一起，使得追查变得混乱(Coinmixing)；
从网络层看: 可以采用多路径转发的方法，数据不直接发送出去，而是经过很多跳(洋葱路由的基本思想)。

## 3.零知识证明

> 零知识证明：一方（证明者）向另一方（验证者）证明某一个陈述是正确的，但不需要透露除该陈述是正确的之外的任何信息。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_tongtaiyincang.png)  
![blockchain_btc_tongtaiyincang](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_tongtaiyincang.png)

    例子：
    Alice想要向Bob证明她知道一组数组x和y使得 x + y = 7,  
    同时不让Bob知道x和y的具体数值。  

1.Alice将E(x)、E(y)发给Bob。（性质2，不可逆）  
2.Bob通过收到的E(x)、E(y)计算得到E(X+Y)。（性质3，同态加法）  
3.Bob进行验证E(x+y)和E(7)是否相等。若相等则验证通过，否则验证失败。（性质1，无碰撞）  
缺陷：Bob可以暴力获取x与y的值。  

## 4.盲签

盲签的关键是不知道内容的情况下做签名。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_mangqian.png)  
![blockchain_btc_mangqian](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_mangqian.png)

## 5.零币和零钞

涉及到很多复杂的密码学和数学知识，而且为了安全会损失很多性能。  
但是再怎么好的匿名性，只要涉及到取款存款就有暴露的风险。  
其实完全的匿名交易，现实生活中没有太多必要，除非做一些坏事。  
比特币的匿名性就足够了。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_lingbi.png)  
![blockchain_btc_lingbi](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_lingbi.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
