---
author: "li_mingxie"
title: "【区块链笔记】BTC_分叉fork(08)"
date: 2022-10-27T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "分叉",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`比特币分叉`相关的内容。  <!--more-->  
  
## 1.状态分叉state fork

挖矿时两个节点差不多同时挖矿发布就会产生临时分叉  
(对比特币系统当前状态产生分歧导致的分叉——state fork)；  

分叉攻击，同样也会导致分叉(forking attack，人为故意造成)也称为deliberate fork。  

## 2.协议分叉protocol fork

比特币协议改变，假设存在少数节点未升级，会出现协议上的分叉(protocal fork)。  
软件升级导致协议不同而产生的分叉有软分叉和硬分叉。  

### 2.1 硬分叉hard fork

如下图把区块的大小从1MB 改成 4MB。  
这导致2个版本的协议相互排斥产生两个硬分叉。  

这时有可能产生上链的交易，回放到另一个链路来攻击偷币。  
以太坊之前真实发生过的事件。(EHT ETC)  
后面给每个链增加了chainId。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_soft_fork_01.png)  
![blockchain_btc_soft_fork_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_soft_fork_01.png)

### 2.2 软分叉soft fork

如下图把区块大小从1MB改成0.5MB。  
这导致软分叉。新的协议不接受旧的区块，旧的区块接受新的区块。  
旧的区块总会废弃掉。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_hard_fork_01.png)  
![blockchain_btc_hard_fork](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_hard_fork_01.png)

比较有名的软分叉是`P2SH(Pay to Script Hash)`。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
