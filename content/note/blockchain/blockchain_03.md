---
author: "li_mingxie"
title: "【区块链笔记】BTC_协议(03)"
date: 2022-10-22T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "共识协议",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`比特币协议`相关内容。  <!--more-->  

## 1.数字货币

### 1.1 中心化的数字货币

举例子央行发行用自己的私钥加密的数字货币。  
这样交易时大家可以用公钥进行验证。  
但是这里需要注意的是会有双花攻击(double spending attack)。  
因为数字货币是可以复制的这是和纸质货币的最大的区别。  

所以我们需要给每个货币做好标记(编号)，
这些货币信息记录在央行的服务器。  
每次消费的时候到服务器做验证，以及相对应的数据调整。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_coin.png)  
![blockchain_coin](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_coin.png?x-oss-process=image/resize,w_400,m_lfit)

### 1.2 去中心化的数字货币

去中心化的数字货币需要解决2个问题。  

1. 如何发行货币：什么时候发行？发多少？
2. 怎么验证交易的有效性，如何防范双花攻击(double spending attack)？

看看比特币是如何解决这两个问题的。  

#### 1.2.1 货币发行问题

比特币是通过挖矿解决的：  
简单的说有很多节点同时打包好交易块，一起计算hash值，直到算出符合条件的hash。  
在计算的过程当中，使用头部的nBit计算出target 目标值。  
不停地更换随机数(nonce)，当随机数满足条件，就算成功了。  
第一个算出来的人，可以打包这个交易块，也可以获得奖励(coinbase)。  
以及打包交易的的交易手续费。  

```
Hash(Block Header) <= target(目标值)
```

> 通过挖矿证明(工作量证明)善意节点，并获取挖矿奖金和区块的记账权利

#### 1.2.2 安全问题(验证有效性)

使用区块的方式解决的：  
每一笔交易都要指明来源于哪笔交易。  
比特币有两个哈希指针：  

1. 连接各块之间，把他们串联起来。  
2. 指向币的来源。  

下面是比特币系统中交易的例子，  

```
A通过挖矿获得10BTC，在下面A将这10BTC转给B 5BTC，转给C 5BTC。  
之后B再转给C 2BTC，转给D 3BTC。当C转给E 7BTC时 C的2个来元交易都需要指明。  
这样就有效的预防双花攻击(double spending attack)
```

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_protocol.png)  
![blockchain_protocol](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_protocol.png)

> 每次交易需要验证上一次交易的有效性以及有没有重复消费。
> 这样可以有效的预防重复消费问题。

## 2.交易需要的信息

每个账户都可以发布交易，比特币区块链可以看做账本，交易广播给每个区块，  
每个人都在自己本地的区块链上写入交易，  
账本的内容要取得分布式的共识（distributed consensus）。  

如果A给B转账，需要的信息是：  
**B的地址**：地址是通过公钥的哈希值算出来的。
**A的公钥**：因为需要验证A的签名，A的公钥是跟交易单一起记录。  

当挖矿成功后，会把打包好的区块后分发给其他模块，  
其他模块验证头部信息符合要求，body交易是否合法，比如有没有签名，有没有重复花等等。  

> 每次交易的过程是需要认证签名以及hash值的对比来做隐私的保护和交易的安全性。

## 3.分叉

有恶意的节点或当有两个区块同时挖出来的时候会产生分叉。  
分叉可以分软分叉，临时分叉，硬分叉。  

**软分叉**是当版本升级后，有些区块没及时升级导致，  
新版本不接受旧版本，旧版本接受新版本的现象。  

**临时分叉**是有时候，同时算出区块的时候，会产生临时分叉。  
过了一些时间后，都会接最常的哪个链。  

**硬分叉**人为的区分分叉。比如比特币有名的Dao事件产生的BTC和BCC。  

一般情况下有分叉，过了一段时间后，区块会自动的修复，扩展最长合法链，把短的分叉废弃掉。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_bitcoin_fork.png)  
![blockchain_coin](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_bitcoin_fork.png)

> 认可和追随最长链，只要一半以上的算力是诚实节点，这个系统就是安全的。

## 4.最后

以太坊是基于账户模式(accoubnbt-based ledger)  
比特币是基于交易模式(Transaction based ledger)  
所以每笔的交易单组成比特币区块，这些区块相互串起来形成比特币的区块链。  
这些简洁的协议有效的做交易单验证，防重复支付和恶意节点攻击。  
下一篇介绍比特币的交易的详解。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
