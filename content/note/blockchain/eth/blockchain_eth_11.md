---
author: "li_mingxie"
title: "【区块链笔记】ETH_geth搭建联盟链(11)"
date: 2022-11-19T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`geth搭建联盟链`相关的信息。  <!--more-->  

## 1.准备genesis.json

```
{
  "alloc": { // 创世时，一开始的代币分配（非必须）
  },
  "config": {
    "chainId": 6668,  // 该链的ID。在用geth 启动区块链时，还需要指定一个network 参数。只有当network、chainID、创世区块配置都相同时，才是同一条链。
    "homesteadBlock": 0, // 相关协议机制的升级区块所在的高度，签名算法是homestead ->eip155 -> eip158，所以从homesteadBlock 之前区块都通过homestead 相关算法机制来验证，homesteadBlock 到eip155Block 之间的用eip155 算法来验证，依次类推
    "eip150Block": 0,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "ethash": {}
  },
  "nonce": "0x000000000000002a", // 预定一个随机数，这是一个与PoW 机制有关的值
  "difficulty": "0x020000",  // 定义了每次挖矿时，最终确定nonce 的难度
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000", // 一个与PoW 机制有关的值
  "coinbase": "0x0000000000000000000000000000000000000000", // 每挖出一个区块，都会获得奖励。该值指定默认情况下把奖励给到哪个账户。实际上，我们每次挖矿开始之前，都会自己指定miner.setEtherbase(UserAddress)，一般都会把奖励给自己
  "timestamp": "0x00", // 时间戳，规定创世区块开始的时间
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000", // 在区块链中，区块是相连的，parentHash 指定了本区块的上一个区块Hash。对于创世区块来说，parentHash 为0
  "extraData": "0x", // 扩展数据
  "gasLimit": "0x2fefd8" // 规定该区块链中，gas 的上限
}
```

## 2.启动节点

```
# 初始化
geth --datadir /Users/limingxie/blockchain/eth/data0 init /Users/limingxie/blockchain/eth/data0/genesis.json

# 启动
geth --identity "node0" --datadir "/Users/limingxie/blockchain/eth/data0" --http --http.port 8545 --http.corsdomain="*" --http.addr "0.0.0.0" --http.api "eth,web3,miner,admin,personal,net"  --nodiscover --networkid 133 --allow-insecure-unlock --port 30303 --ipcdisable
```

```
# 指定新的数据目录 新的端口
geth --datadir /Users/limingxie/blockchain/eth/data1 init /Users/limingxie/blockchain/eth/data1/genesis.json
geth --identity "node1" --datadir "/Users/limingxie/blockchain/eth/data1" --http --http.port 8546 --http.corsdomain="*" --http.addr "0.0.0.0" --http.api "eth,web3,miner,admin,personal,net" --nodiscover   --networkid 133 --allow-insecure-unlock --port 30304 --ipcdisable --authrpc.port 8552

# 指定新的数据目录 新的端口
geth --datadir /Users/limingxie/blockchain/eth/data2 init /Users/limingxie/blockchain/eth/data2/genesis.json
geth --identity "node2" --datadir "/Users/limingxie/blockchain/eth/data2" --http --http.port 8547 --http.corsdomain="*" --http.addr "0.0.0.0" --http.api "eth,web3,miner,admin,personal,net" --nodiscover   --networkid 133 --allow-insecure-unlock --port 30305 --ipcdisable --authrpc.port 8553
```

```
# 通过http协议加入进入 console
geth attach http://127.0.0.1:8545
```

### 3.node0的操作

当启动新的两个节点console后，查看enode，到node0上面去加入node1和node2的enode

```
# 在node0操作
> admin.addPeer("enode://5647880cdfd8236224a840942bfe297a3dc2ef8562fd96af587419b867e899de47ec3cd7affdd24ee353721071524245094446d0f5d28567ebbf69872f410f04@127.0.0.1:30304?discport=0")
true
> admin.addPeer("enode://ce02e7c69f2a651214a36e16637543a2910eb81617f0989b8c259765ae6e44765b08a6888d7af175966ed6391f917e6d588c1238441886581152362c11c08f38@127.0.0.1:30305?discport=0")
true

# 查看加入的节点
admin.peers 
```

成功加入以后在node0操作如下

```
# 创建2个账户
> personal.newAccount("1234")
"0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2"
> personal.newAccount("12345")
"0xc031223c0512f02ba3b6f7b715bce52a96c543db"

# 设置矿工 返回true
> miner.setEtherbase("0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2")
true
> eth.coinbase
"0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2"

# 开始挖矿
> miner.start(1)
null
> eth.blockNumber
8

# 停止挖矿
> miner.stop()
null

# 查看以太币数
> web3.fromWei(eth.getBalance('0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2'),'ether');
20

# 转账失败
> eth.sendTransaction({from:"0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2", to: "0xc031223c0512f02ba3b6f7b715bce52a96c543db", value: 5000000000000000000})
Error: authentication needed: password or unlock
 at web3.js:6365:9(45)
 at send (web3.js:5099:62(34))
 at <eval>:1:20(10)

# 解锁账户
> personal.unlockAccount("0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2")
Unlock account 0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2
Passphrase:
true

# 创建2笔转账交易
> eth.sendTransaction({from:"0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2", to:"0xc031223c0512f02ba3b6f7b715bce52a96c543db", value:5000000000000000000})
"0xbdba60003be83c884efea837947a12ce695cb546afc57e8f153fe50668999470"
> eth.sendTransaction({from:"0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2", to:"0xc031223c0512f02ba3b6f7b715bce52a96c543db", value:2000000000000000000})
"0xd178aa7207b3fd06b8925eaa804e04e1adb4d794757c09f20e58e8cfbef466b8"

# 挖矿  挖矿了才能打包上述2笔交易
> miner.start(1)
null

# 停止挖矿
> miner.stop()
null

# 查看账户余额
> web3.fromWei(eth.getBalance('0xc031223c0512f02ba3b6f7b715bce52a96c543db'),'ether');
7
```

## 4.node1查看数据同步情况

先进入node1的控制台

```
geth attach http://127.0.0.1:8546
```

```
# 查看block高度
> eth.blockNumber
19

# 余额已经同步过来了
> web3.fromWei(eth.getBalance('0xc031223c0512f02ba3b6f7b715bce52a96c543db'),'ether');
7

# 查看tx
> eth.getTransaction("0xbdba60003be83c884efea837947a12ce695cb546afc57e8f153fe50668999470")
{
  blockHash: "0x5cf8e182ef0ce4ae90c22e48973b56f26e4eb124eb3a5c989cd4be8d13cecda1",
  blockNumber: 11,
  chainId: "0x53a",
  from: "0xc8a840a45ce3583b59244eb0b4ef2ec9e1bef8f2",
  gas: 21000,
  gasPrice: 1000000000,
  hash: "0xbdba60003be83c884efea837947a12ce695cb546afc57e8f153fe50668999470",
  input: "0x",
  nonce: 0,
  r: "0xe372ebf954306e4efaec4241ad543625b56cfcf4a5be4e584f45aadcf5563fd8",
  s: "0x2fe76455e50a72e7abf8e020c60f023731a67030994f06b45e69396a6b67a910",
  to: "0xc031223c0512f02ba3b6f7b715bce52a96c543db",
  transactionIndex: 0,
  type: "0x0",
  v: "0xa97",
  value: 5000000000000000000
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
