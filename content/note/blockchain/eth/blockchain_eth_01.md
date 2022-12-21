---
author: "li_mingxie"
title: "【区块链笔记】ETH_账户和交易(01)"
date: 2022-11-08T23:28:49+08:00
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

这篇文章简单的整理了`以太坊账户`相关的内容。  <!--more-->  

## 1.ETH账户

比特币是基于交易的账本transaction-based ledger，  
而以太坊是基于账户的账本account-based ledger。  
计算余额的时候，比特币是需要通过UTXO算出来的。  
而以太坊是直接查询账户余额即可。  

#### 重放攻击（replay attack)  

比特币是需要防止双花攻击double spending attack。 是花钱方恶意的攻击。  
而以太坊的重放攻击是收钱方恶意的想重复的收钱的攻击。  
想防止这种攻击也不难，给每个账户添加nonce，给每次交易的次数即可。  

    A -> B  
    nonce = 21
    signed by A

## 2.账户类型

**外部账户(Externally owned account, EOA)**：  
    类似于BTC系统中公私钥对。存在账户余额balance和计数器nonce  

**合约账户(Contract accounts)**：  
    并非通过公私钥对控制，指智能合约的账户地址。  
    (不能主动发起交易，只能接收到外部账户调用后才能发起交易或调用其他合约账户)  
    其除了balance和nonce之外还有code(代码)、storage(相关状态-存储)  

在以太坊账户中，我们维护以下几个状态：  

nonce：外部账户为交易次数，合约账户为创建的合约序号。  
balance：此地址的以太币余额。  
storageRoot：账户存储内容组成的默克尔树根的哈希值。  
codeHash：账户EVM代码的hash值。合约账户即为合约代码的哈希值，外部账户为空字符串的哈希值。  

## 3.账户模型的优点

#### UTXO优点

* 私密性比较强，理论上可以为每一笔输出设置一个地址。  
* 无需维护余额等状态值。  
* UTXO是独立数据记录，可以通过并行极大的提升区块链交易验证速度。  
* 无需关心事务问题，只需要关心输出脚本即可。  
* UTXO无法分割，每次交易都需要多个输出，分别为支出与找零。  

#### 账户模型优点

* 可以快速获取账户的余额，而比特币需要将指定地址所拥有的所有UTXO中的未花费交易总值整合。  
* 节省空间，因为每笔交易只有一个输入一个输出。  
* 可以较容易的实现图灵完备的智能合约。  

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/eth_account_001.png)  
![eth_block](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/eth_account_001.png)  

## 4.交易Transaction

简单的说交易就是一个  
签名的数据包，由EOA发送到另一个账户  
包含

* 消息的接收方地址
* 发送方签名
* 金额(VALUE)
* 数据(DATA，可选)
* START GAS
* GAS PRICE

## 5.消息Message

合约账户向合约账户发送的叫“消息”  
消息是不会被序列化的虚拟对象，只存在于以太坊执行环境 (EVM)中  
可以看作函数调用  
包含  

* 消息发送方
* 消息接收方
* 金额(VALUE)
* 数据(DATA，可选)
* START GAS

## 6.合约Contract

* 可以读/写自己的内部存储(32字节key-value的 数据库)
* 可向其他合约发送消息，依次触发执行
* 一旦合约运行结束，并且由它发送的消息触发的  
    所有子执行(sub-execution)结束，EVM就会中 止运行，直到下次交易被唤醒

## 7.合约应用Dapp

* 数据存储，数字货币(代币)
* 转发合同，有限制的合同
* 管理多个账户用户之间的合同

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
