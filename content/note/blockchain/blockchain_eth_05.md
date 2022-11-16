---
author: "li_mingxie"
title: "【区块链笔记】ETH_挖矿算法和难度调整(05)"
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

这篇文章简单的整理了`以太坊的挖矿算法和难度调整`相关的内容。  <!--more-->  

## 1.ASIC Resistance

因BTC后续的挖矿都是使用专用挖矿ASIC，违背了中本聪起初的人人使用家用电脑挖矿的初衷。  
后来挖矿都想着ASIC Resistance(抗ASIC)，常用的方法是Memory Hard Mining Puzzle。  
这里比较有名的是`莱特币LiteCoin`的基于Scrypt的算法。

## 2.Scrypt算法

scrypt是一种密码衍生算法。  
密码衍生算法主要作用就是根据初始化的主密码来生成系列的衍生密码。  
这种算法主要是为了抵御暴力破解的攻击。通过增加密码生成的复杂度，同时也增加了暴力破解的难度。

1. 设置一个很大的数组，按照顺序填充伪随机数

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_scrypt_01.png)  
![eth_scrypt_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_scrypt_01.png)

2. 在需要求解Puzzle的时候，按照伪随机顺序，从数组中读取一些数，每次读取位置与前一个数相关

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_scrypt_02.png)  
![eth_scrypt_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_scrypt_02.png)

使用消耗很多内存的方式实现了ASIC机的不友好。

## 3.以太坊

以太坊也采用了Memory Hard Mining Puzzle

为了便于进行验证，以太坊使用了两个数据集。  
轻节点保存16MB的Cache进行验证即可，而矿工为了挖矿更快，减少重复计算则需要存储1GB大小的大数据集。

1. 通过Seed进行一些运算获得第一个数，之后每个数字都是通过前一个位置的值取哈希获得的
2. 先生成一个更大的数组(以太坊中这两个数组大小并不固定，因为考虑到计算机内存不断增大，因此该两个数组需要定期增大)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_01.png)  
![eth_mining_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_01.png)

3. 大的DAG生成方式

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_02.png)  
![eth_mining_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_02.png)

### 伪代码

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_01.png)  
![eth_mining_code_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_01.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_02.png)  
![eth_mining_code_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_02.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_03.png)  
![eth_mining_code_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_03.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_04.png)  
![eth_mining_code_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_04.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_05.png)  
![eth_mining_code_05](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_05.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mining_code_06.png)  
![eth_mining_code_06](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mining_code_06.png)

## 4.权益证明(POS: Proof of State)

按照所占权益投票进行共识达成，类似于股份制有限共识按照股份多少投票，权益证明不需要挖矿。
因为ASIC芯片研发周期很长，成本很高，这对ASIC矿机厂商来说，是一个很大的风险。  

## 5.预挖矿(Pre-Mining)

以太坊中采用的预挖矿的机制。这里“预挖矿”并不挖矿，而是在开发以太坊时，给开发者预留了一部分货币。  
以太坊的早期开发者，目前就很有钱了
  
----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
