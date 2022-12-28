---
author: "li_mingxie"
title: "【区块链笔记】ETH_geth控制台操作(10)"
date: 2022-11-18T23:28:49+08:00
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

这篇文章简单的整理了`geth控制台操作`相关的信息。  <!--more-->  

## 1.启动控制台

启动命令里添加console就可以进入控制台  

```
$geth --datadir /Users/limingxie/blockchain/eth/test1_data --networkid 66666 console
```

```
//--http 是连接localhost:8545   console 是打开控制台   2>output.log 是输出日志
$geth --datadir /Users/limingxie/blockchain/eth/test1_data --networkid 66666 --http console 2>output.log
```

|参数|说明|
|---|---|
| **`--datadir`** | 指定节点数据目录 |
| **`init`** | 指定初始化节点使用的配置文件 genesis.json |
| **`--identity`** | 设定节点标识 |
| **`--http`** | 开启http rpc 服务 |
| **`--http.port`** | 指定http rpc端口 |
| **`--http.corsdomain`** | 指定跨域 |
| **`--http.addr`** | 监听地址，默认为127.0.0.1，只能本地访问 |
| **`--http.api`** | 设置节点上启用RPC接口 |
| **`--nodiscover`** | 使用此选项可确保未手动添加您的人员无法发现您的节点。否则，如果您的节点具有相同的创世纪文件和网络ID，则可能无意中将您的节点添加到陌生人的区块链中 |
| **`--networkid`** | 设定网络ID，当创建的链的 genesis block 和 network id 刚好与网络上其他人的链相同，那么就看哪条链长，如果比对方的短，那么链上的数据会全部被覆盖，变成对方的链。 |
| **`--allow-insecure-unlock`** | 允许使用 http 协议进行账户解锁 |
| **`--port`** | 网络侦听端口，对等端连接端口 |
| **`--ipcdisable`** | 指定跨域 |

> 启动是加 --dev 回忆dev模式启动，会节省许多繁杂操作，比如省略挖矿等操作，专注于开发.

## 2.JavaScript 对象

Geth Console 是一个交互式的 JavaScript 执行环境，里面内置了一些用来操作以太坊的 JavaScript对象，  
我们可以直接调用这些对象来获取区块链上的相关信息。

> 所有的对象是几乎都被web3对象包含

* **`eth`**:对区块链进行访问和交互相关的方法;
* **`net`**:网络操作查看
* **`p2p`** 网络状态的方法;
* **`admin`**:管理节点相关的方法;
* **`miner`**:挖矿相关的一些方法;
* **`personal`**:包含账户管理的方法;
* **`txpool`**:查看交易内存池的方法;
* **`web3`**:包含以上所有对象，还包含一些通用方法。

## 3.常用命令

* **`personal.newAccount()`**:创建账户;
* **`personal.unlockAccount()`**:解锁账户;
* **`eth.accounts`**:列出系统中的账户;
* **`eth.getBalance()`**:查看账户余额，返回值的单位是 Wei;
* **`eth.blockNumber`**:列出当前区块高度;
* **`eth.getTransaction()`**:获取交易信息;
* **`eth.getBlock()`**:获取区块信息;
* **`miner.start()`**:开始挖矿;
* **`miner.stop()`**:停止挖矿;
* **`web3.fromWei()`**:Wei 换算成以太币;
* **`web3.toWei()`**:以太币换算成 Wei;
* **`txpool.status`**:交易池中的状态;

## 4.实际操作

```
//查看账户余额：
> eth.getBalance('0x922F12edF3CA38CF04bD5f0d6a1b716C51f5G3ab')
6.66001e+27

> web3.fromWei(eth.getBalance('0x922F12edF3CA38CF04bD5f0d6a1b716C51f5G3ab'),'ether');
6660010000

//查看当前区块
> eth.blockNumber
0

//创建新用户
> personal.newAccount()
Passphrase:
Repeat passphrase:
INFO [12-15|23:24:45.034] Your new key was generated               address=0xAd9E42f712F152a8E9F780e6A23D9AFb2a020607
WARN [12-15|23:24:45.035] Please backup your key file!             path=/Users/limingxie/blockchain/eth/test1_data/keystore/UTC--2022-12-15T15-24-43.534890000Z--ad9e42f712f152a8e9f780e6a23d9afb2a020607
WARN [12-15|23:24:45.035] Please remember your password!
"0xad9e42f712f152a8e9f780e6a23d9afb2a020607"

//查看创建用户
> eth.accounts
["0xad9e42f712f152a8e9f780e6a23d9afb2a020607"]

//新的终端查看用户文件信息
$ cat /Users/limingxie/blockchain/eth/test1_data/keystore/UTC--2022-12-15T15-24-43.534890000Z--ad9e42f712f152a8e9f780e6a23d9afb2a020607
{"address":"ad9e42f712f152a8e9f780e6a23d9afb2a020607","crypto":{"cipher":"aes-128-ctr","ciphertext":"fa0ede4585b510173ab438384740f3386e3df19d934aac5c26ace221d9f47ba8","cipherparams":{"iv":"293bc427b035f0943b4bbb965c907293"},"kdf":"scrypt","kdfparams":{"dklen":32,"n":262144,"p":1,"r":8,"salt":"d88a19c15fd6c1234d2ebf2d1792571d53ac3ae0829b2ecdcfa8a0d9dd805170"},"mac":"27ef6a66329edc85ada55946562907a64d9963cb7dee919b086e7f4c75e1ad6a"},"id":"1c5ce5e3-6e54-4bfc-b6d9-b6414f462b08","version":3}

//查看新创建用户的余额
> eth.getBalance(eth.accounts[0]);
0

//发送以太币
> eth.sendTransaction({from: eth.accounts[0], to: "0x922F12edF3CA38CF04bD5f0d6a1b716C51f5G3ab", value: 100000})
WARN [12-15|23:51:27.506] Served eth_sendTransaction               reqid=134 duration="64.833µs"  err="insufficient funds for transfer"
Error: insufficient funds for transfer

//查看获取coinbase账户
> eth.coinbase
"0xad9e42f712f152a8e9f780e6a23d9afb2a020607"

//开始挖矿
> miner.start(1)
INFO [12-15|23:53:55.489] Updated mining threads                   threads=1
INFO [12-15|23:53:55.495] Transaction pool price threshold updated price=1,000,000,000
INFO [12-15|23:53:55.495] Etherbase automatically configured       address=0xAd9E42f712F152a8E9F780e6A23D9AFb2a020607

//停止挖矿
> miner.stop()

//再次查看以太币
> web3.fromWei(eth.getBalance(eth.accounts[0]),'ether');
24

//再次转账
> eth.sendTransaction({from: eth.accounts[0], to: "0x922F12edF3CA38CF04bD5f0d6a1b716C51f5G3ab", value: 100000})
WARN [12-16|00:17:37.011] Gas estimation capped by limited funds   original=138,961,213,547,761 balance=24,000,000,000,000,000,000 sent=100,000 maxFeePerGas=1,000,000,000 fundable=23,999,999,999
WARN [12-16|00:17:37.012] Caller gas above allowance, capping      requested=23,999,999,999 cap=50,000,000
WARN [12-16|00:17:37.015] Served eth_sendTransaction               reqid=148 duration=10.576325ms err="authentication needed: password or unlock"
Error: authentication needed: password or unlock

//解锁账户
> personal.unlockAccount(eth.accounts[0]);
Unlock account 0xad9e42f712f152a8e9f780e6a23d9afb2a020607
Passphrase: 
true

//再次发送以太币
> eth.sendTransaction({from: eth.accounts[0], to: "0x922F12edF3FA36CF04bD5f0d6a1b716C51f5F4ab", value: 100000000000000})
WARN [12-16|00:20:14.592] Gas estimation capped by limited funds   original=138,961,213,547,761 balance=23,999,999,999,999,900,000 sent=100,000,000,000,000 maxFeePerGas=1,000,000,000 fundable=23,999,899,999
WARN [12-16|00:20:14.592] Caller gas above allowance, capping      requested=23,999,899,999 cap=50,000,000
INFO [12-16|00:20:14.593] Submitted transaction                    hash=0xe0786ce32a79642dbcf0419946c705a514385886c4c2f65e089a3f4c9f8c3d1f from=0xAd9E42f712F152a8E9F780e6A23D9AFb2a020607 nonce=1 recipient=0x922F12edF3FA36CF04bD5f0d6a1b716C51f5F4ab value=100,000,000,000,000
"0xe0786ce32a79642dbcf0419946c705a514385886c4c2f65e089a3f4c9f8c3d1f"

//开始挖矿
> miner.start(1)
INFO [12-16|00:20:27.876] Updated mining threads                   threads=1
INFO [12-16|00:20:27.876] Transaction pool price threshold updated price=1,000,000,000

//停止挖矿
> miner.stop()

//查看余额减少
> web3.fromWei(eth.getBalance(eth.accounts[0]),'ether');
43.9998999999999

//因为转了2次 100000 和 100000000000000
> eth.getBalance('0x922F12edF3FA36CF04bD5f0d6a1b716C51f5F4ab')
100000000100000

//查看tx
> eth.getTransaction("0xe0786ce32a79642dbcf0419946c705a514385886c4c2f65e089a3f4c9f8c3d1f")
{
  blockHash: "0x7f7340215d6a57d35101499326f40648e83a0bc90156af9e9f8be773573e6cf1",
  blockNumber: 13,
  chainId: "0x1046a",
  from: "0xad9e42f712f152a8e9f780e6a23d9afb2a020607",
  gas: 21000,
  gasPrice: 1000000000,
  hash: "0xe0786ce32a79642dbcf0419946c705a514385886c4c2f65e089a3f4c9f8c3d1f",
  input: "0x",
  nonce: 1,
  r: "0x53413e893ae7f53dab195c4a0dc7785e0260430da5ef2b33f227dde2fe6d9e4e",
  s: "0x3e61756ce5b5e9192679f73c4e5788929e8f7c7c2411041fa3231bc7327e479d",
  to: "0x922f12edf3fa36cf04bd5f0d6a1b716c51f5f4ab",
  transactionIndex: 1,
  type: "0x0",
  v: "0x208f8",
  value: 100000000000000
}

//查看block
> eth.getBlock(13)
> eth.getBlock("0x7f7340215d6a57d35101499326f40648e83a0bc90156af9e9f8be773573e6cf1")
{
  difficulty: 131072,
  extraData: "0xd983010a1a846765746888676f312e31382e318664617277696e",
  gasLimit: 138961213547761,
  gasUsed: 42000,
  hash: "0x7f7340215d6a57d35101499326f40648e83a0bc90156af9e9f8be773573e6cf1",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0xad9e42f712f152a8e9f780e6a23d9afb2a020607",
  mixHash: "0xdeb5d843bc08a9538bbf54e9cb12ee24b5bcd99f0cc644e9b78f7220dd2f5539",
  nonce: "0x654ead449a293d84",
  number: 13,
  parentHash: "0xdc42d2a5401228ec99eed3d067ba2aadbcd2b0f5dccc957c99024b9d29c75247",
  receiptsRoot: "0xd95b673818fa493deec414e01e610d97ee287c9421c8eff4102b1647c1a184e4",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 760,
  stateRoot: "0xb349e2f94cd7cb155543465436c756342d9565093141d81575c31af36bc6c3ba",
  timestamp: 1671121227,
  totalDifficulty: 1707840,
  transactions: ["0xca7ba0e0689e35244a452d263ae29dc163b0450f78fa6be025b8df320f9afdff", "0xe0786ce32a79642dbcf0419946c705a514385886c4c2f65e089a3f4c9f8c3d1f"],
  transactionsRoot: "0x1589a9bcdd1fa40c4cf22f9ddebe73fe5230ba3b034dbb9401ceaab069646638",
  uncles: []
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
