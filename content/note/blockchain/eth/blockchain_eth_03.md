---
author: "li_mingxie"
title: "【区块链笔记】ETH_交易树和收据树(03)"
date: 2022-11-10T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`以太坊交易树和收据树`相关的内容。  <!--more-->  
太坊区块数据有三棵树，状态树，交易树和收据树。这里主要整理状态树。  

## 1.交易树和收据树

交易树和收据树的用途：

* 向轻节点提供Merkle Proof。
* 更加复杂的查找操作(例如：查找过去十天的交易；过去十天的众筹事件等)  

交易树和收据树是只保存一段时间内的交易和收据信息。  
状态树是存所有账号的状态信息。状态树之所以不能只存变化信息的最大原因是  
给新的账户转账时，就需要搜索到创始块后才能确定，该账户是否存在。  

## 2.Bloom filter(布隆过滤器)

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/bloom-filter.png)  
![bloom-filter](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/bloom-filter.png?x-oss-process=image/resize,w_400,m_lfit)  

> Bloom filter特点：有可能出现误报，但不会出现漏报。  
> Bloom filter变种：采用一组哈希函数进行向量映射，有效避免哈希碰撞  

Bloom filter不支持删除操作。如果想要支持删除操作，需要将记录数不能为0和1。  

## 3.具体结构

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_newblock_01.png)  
![eth_newblock](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_newblock_01.png)  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
