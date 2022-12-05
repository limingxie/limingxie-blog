---
author: "li_mingxie"
title: "【区块链笔记】ETH_GHOST协议(04)"
date: 2022-11-11T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`以太坊GHOST协议`相关的内容。  <!--more-->  

## 1.GHOST协议

以太坊的出块时间是10+秒，这样会比比特币大大加快了系统反应速度。  
作为代价系统的临时分叉会成为常态。这样很有可能挖到的矿会经常会废弃掉。  
而且个人比起矿池劣势就很明显了。  
对此以太坊设计了GHOST协议。  

## 2.Uncle Block奖励和打包奖励

1. Uncle Block(A1,A2)获得7/8的出块奖励
2. A打包时最多只能包含2个Uncle Block.
3. 旷工获得1/32的奖励。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_ghost_01.png)  
![eth_ghost_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_ghost_01.png?x-oss-process=image/resize,w_500,m_lfit)  

>为了找一个难度低的模块区间，不定的挖矿获得Uncle Block奖励。  
>以太坊规定每上层的Uncle Block奖励也相对应的减少，到2/8后就没有奖励了。  
如图所示A所包含的层级不同，给的挖矿奖励也逐渐的减少：  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_ghost_02.png)  
![eth_ghost_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_ghost_02.png)

>Uncle Block奖励只给下级模块(A2)，后续的模块是不会给奖励。  
>如果后续的区块(A3,A4...)也给奖励的话，分叉攻击的损失也会降低。  
>分叉攻击的成本不高，那间接的提高了分叉攻击的风险。  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_ghost_03.png)  
![eth_ghost_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_ghost_03.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
