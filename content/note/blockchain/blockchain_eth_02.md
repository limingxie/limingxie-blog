---
author: "li_mingxie"
title: "【区块链笔记】ETH_状态树(02)"
date: 2022-11-09T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
    "account",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`以太坊状态树`相关的内容。  <!--more-->  
太坊区块数据有三棵树，状态树，交易树和收据树。这里主要整理状态树。  

## 1.ETH状态树

在以太坊中，账户地址为160位，表示为40个16进制数额。  
状态包含了余额(balance)、交易次数(nonce),合约账户中还包含了code(代码)、存储(stroge)。  

## 2.MPT(Merkle Patricia tree)

MPT实际上是三种数据结构的组合，分别是Trie树， Patricia Trie， 和Merkle树。  

### Trie树

Trie树，又称字典树，单词查找树或者前缀树，是一种用于快速检索的多叉树结构，  
如英文字母的字典树是一个26叉树，数字的字典树是一个10叉树。  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_trie.png)  
![eth_trie](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_trie.png?x-oss-process=image/resize,w_350,m_lfit)  

### Patricia Trie

Patricia trie就是进行了路径压缩的trie。  
如上图例子，进行路径压缩后如下图所示  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_patricia_trie.png)  
![eth_patricia_trie](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_patricia_trie.png?x-oss-process=image/resize,w_400,m_lfit)  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_patricia_trie_01.png)  
![eth_patricia_trie](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_patricia_trie_01.png?x-oss-process=image/resize,w_400,m_lfit)

以太坊中针对MPT(Merkle Patricia tree)进行了修改，称其为MPT(Modified Patricia tree)  
下图为以太坊中使用的MPT结构示意图。右上角表示四个账户(为直观，显示较少)和其状态(只显示账户余额)。  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mpt_01.png)  
![eth_mpt](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mpt_01.png)  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_mpt.png)  
![eth_mpt](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_mpt.png)  

## 3.数据结构

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_header_01.png)  
![eth_header_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_header_01.png)  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_block_01.png)  
![eth_block](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_block_01.png)  

区块在网上真正发布时的信息

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_extblock.png)  
![eth_extblock](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_extblock.png)  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
