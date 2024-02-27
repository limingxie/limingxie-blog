---
author: "li_mingxie"
title: "【区块链】以太坊2.0权益证明_共识过程(03)"
date: 2099-12-19T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
    "ethereum",
    "white paper",
]
categories: [
    "eth",
    "blockchain",
]
---

上一篇文章[【区块链】以太坊2.0权益证明_信标链(02)](https://limingxie.github.io/blockchain/eth/eth_2_0/ethereum_pos_2/)  
介绍了以太坊2.0信标链相关的基本概念,这一篇详细的整理共识的过程。  <!--more-->  

2. 一个P2P网络还是两个P2P网络？
是2个P2P网络。

ETH1除了剥离出共识层外，其它工作内容是不变的。所以，EOA账户发起的交易（tx）发送到某个ETH1节点，节点仍旧会把该交易广播到整个执行层P2P网络，所有节点维护自己的交易池（tx pool），根据共识层的指令执行和打包交易到区块，并广播区块到执行层的P2P网络。

Beacon chain每个slot进行一轮共识，某个验证者（validator）被选定为区块提案人（proposer），该提案人节点（beacon node，简称BN）通过RPC访问自己的ETH1 node，让ETH1 node生成出一个ETH1 block，提案人把ETH1 block包裹（wrapped）到自己的beacon block当中，然后广播到整个共识层P2P网络。

共识层的其它节点收到区块信息后，共识层负责校验、执行共识数据（签名投票，区块Finalty状态等）、并更新共识层状态。执行层负责校验、执行执行层区块中的交易（智能合约调用，用户间转账等）并更新状态树。


**参考**  

<https://ethos.dev/beacon-chain>
<https://zhuanlan.zhihu.com/p/625003066>

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
