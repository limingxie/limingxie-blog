---
author: "li_mingxie"
title: "【区块链笔记】ETH_挖矿难度调整(06)"
date: 2022-11-13T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
]
---

这篇文章简单的整理了`以太坊的挖矿难度调整`相关的内容。  <!--more-->  

比特币的难度是每隔2016个区块调整一次。而以太坊是每个区块都又可能会进行难度调整。  
以太坊难度调整较为复杂，存在多个版本，网络上存在诸多不一致，这里遵循以代码逻辑为准的原则，从代码中查看以太坊难度调整算法。

## 1.以太坊难度调整

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_001.png)  
![eth_mining_001](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_001.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_002.png)  
![eth_mining_002](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_002.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_003.png)  
![eth_mining_003](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_003.png)

## 2.难度炸弹

以太坊在设计之初就计划要逐步从POW（工作量证明）转向POS（权益证明），而权益证明不需要挖矿。  
因此，以太坊在设计之初便添加了难度炸弹，迫使矿工转入POS。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_004.png)  
![eth_mining_004](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_004.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_005.png)  
![eth_mining_005](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_005.png)

## 3.以太坊发展

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_006.png)  
![eth_mining_006](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_006.png)

## 4.具体代码

1. 难度计算公式
bigTime为当前区块时间戳，bigParentTime为当前区块的父区块时间戳。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_001.png)  
![eth_mining_code_001](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_001.png)

2. 基础部分计算

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_002.png)  
![eth_mining_code_002](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_002.png)

3. 难度炸弹计算

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_003.png)  
![eth_mining_code_003](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_003.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
