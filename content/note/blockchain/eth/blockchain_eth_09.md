---
author: "li_mingxie"
title: "【区块链笔记】ETH_搭建私有链(09)"
date: 2022-11-17T23:28:49+08:00
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

这篇文章简单的整理了搭建`eth私有链`。  <!--more-->  

## 1.安装Geth

### 1.1 安装方式的选择

安装私有链可以使用系统包管理器比如(apt-get)，也可以使用源码安装。  
这里主要整理的是源码安装。  

### 1.2 克隆代码

```
git clone git@github.com:ethereum/go-ethereum.git
```

### 1.3 切换分支

```
Releases 165
Paravin (v1.10.26) Latest on Nov 3
```

因为我现在的最新的发布版的标签是v1.10.26  
切换到改分支的代码。(需要留意一下因为是处于“detached HEAD" 状态所以新建一个分支)  

```
$ cd go-ethereum

$ git checkout -b tag_1_10_26 v1.10.26
Switched to a new branch 'tag_1_10_26'
```

### 1.4 构建Geth

```
$ make geth
env GO111MODULE=on go run build/ci.go install ./cmd/geth
go: downloading golang.org/x/net v0.0.0-20220607020251-c690dde0001d
>>> /usr/local/go/bin/go build -ldflags "-X main.gitCommit=e5eb32acee19cc9fca6a03b10283b7484246b15a -X main.gitDate=20221103 -s" -tags urfave_cli_no_docs -trimpath -v -o /Users/limingxie/projects/gopath/src/github.com/ethereum/go-ethereum/build/bin/geth ./cmd/geth
go: downloading github.com/urfave/cli/v2 v2.10.2
go: downloading github.com/status-im/keycard-go v0.0.0-20190316090335-8537d3370df4
go: downloading github.com/deckarep/golang-set v1.8.0
go: downloading github.com/rjeczalik/notify v0.9.1
go: downloading github.com/hashicorp/golang-lru v0.5.5-0.20210104140557-80c98217689d
... ... ...
... ... ...
Done building.
Run "./build/bin/geth" to launch geth.
```

> 也可以把"./build/bin"路径添加到环境变量的路径，直接使用geth命令。  

### 1.5 查看版本以及启动

```
$ cd build/bin

$ ./geth version
Geth
Version: 1.10.26-stable
Git Commit: e5eb32acee19cc9fca6a03b10283b7484246b15a
Git Commit Date: 20221103
Architecture: amd64
Go Version: go1.18.1
Operating System: darwin
GOPATH=/Users/limingxie/projects/gopath
GOROOT=go
```

如下启动会启动全节点，会不停的下主网数据，启动后感受一下，最好关了...^^  

```
$ ./geth -datadir  /Users/limingxie/blockchain/eth/data
INFO [12-15|11:02:47.308] Starting Geth on Ethereum mainnet...
INFO [12-15|11:02:47.308] Bumping default cache on mainnet         provided=1024 updated=4096
INFO [12-15|11:02:47.310] Maximum peer count                       ETH=50 LES=0 total=50
INFO [12-15|11:02:47.315] Set global gas cap                       cap=50,000,000
INFO [12-15|11:02:47.317] Allocated trie memory caches             clean=614.00MiB dirty=1024.00MiB
INFO [12-15|11:02:47.317] Allocated cache and file handles         database=/Users/limingxie/blockchain/eth/data/geth/chaindata cache=2.00GiB handles=5120
INFO [12-15|11:02:47.447] Opened ancient database                  database=/Users/limingxie/blockchain/eth/data/geth/chaindata/ancient/chain readonly=false
INFO [12-15|11:02:47.447] Writing default main-net genesis block
INFO [12-15|11:02:47.717] Persisted trie from memory database      nodes=12356 size=1.78MiB time=26.267747ms gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
... ... ...
... ... ...
INFO [12-15|11:02:47.786] Started P2P networking                   self=enode://c6b1cb292108bde53130ed2c5a1341a0c5650473ee9c70fb39e35bde05e7e06f435dd5c4c3c657d910e87b32297d89f7a32bdf3db2f9fdb1432d5edc14e1450e@127.0.0.1:30303
INFO [12-15|11:02:47.788] IPC endpoint opened                      url=/Users/limingxie/blockchain/eth/data/geth.ipc
INFO [12-15|11:02:47.788] Generated JWT secret                     path=/Users/limingxie/blockchain/eth/data/geth/jwtsecret
INFO [12-15|11:02:47.820] Generated state snapshot                 accounts=8893 slots=0 storage=409.64KiB dangling=0 elapsed=85.903ms
INFO [12-15|11:02:50.583] Mapped network port                      proto=tcp extport=30303 intport=30303 interface="UPNP IGDv1-IP1"
INFO [12-15|11:02:50.790] New local node record                    seq=1,671,073,367,786 id=3059c066a7ec5ceb ip=192.168.1.3 udp=30303 tcp=30303
INFO [12-15|11:02:52.791] WebSocket enabled                        url=ws://127.0.0.1:8551
INFO [12-15|11:02:52.791] HTTP server started                      endpoint=127.0.0.1:8551 auth=true prefix= cors=localhost vhosts=localhost
INFO [12-15|11:02:57.865] Looking for peers                        peercount=0 tried=17 static=0
```

## 2.节点同步

* --syncmode full ==> 全部数据和验证
* --syncmode fast ==> 只同步数据，不验证
* --syncmode light ==> 轻节点模式，仅获取当前状态

```
$./geth --datadir /Users/limingxie/blockchain/eth/data --syncmode light
```

* --testnet ==> 测试网络

```
$./geth --testnet --datadir /Users/limingxie/blockchain/eth/data --syncmode fast
```

## 3.搭建私有链

### 3.1 创建网络的“创世”(genesis)状态

genesis.json

```json
{
    "config": {
        "chainId": 2222,
        "homesteadBlock": 0,
        "eip150Block": 0,
        "eip155Block": 0,
        "eip158Block": 0,
        "byzantiumBlock": 0,
        "constantinopleBlock": 0,
        "petersburgBlock": 0,
        "istanbulBlock": 0
    },
    "alloc": {
        "7df9a875a174b3bc565e6424a0050ebc1b2d1d82": {
            "balance": "300000"
        },
        "f41c74c9ae680c1aa78f42e5647a62f353b7bdde": {
            "balance": "400000"
        }
    },
    "coinbase": "0x0000000000000000000000000000000000000000",
    "difficulty": "0x2000",
    "extraData": "",
    "gasLimit": "0x2fefd8",
    "nonce": "0x0000000000000042",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp": "0x00"
}
```

### 3.2 创建区块链

```
$ geth --datadir /Users/limingxie/blockchain/eth/data_test1 init /Users/limingxie/blockchain/eth/data_test1/genesis.json
INFO [01-06|15:43:36.418] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-06|15:43:36.423] Set global gas cap                       cap=50,000,000
INFO [01-06|15:43:36.424] Allocated cache and file handles         database=/Users/limingxie/blockchain/eth/data_test1/geth/chaindata cache=16.00MiB handles=16
INFO [01-06|15:43:36.528] Opened ancient database                  database=/Users/limingxie/blockchain/eth/data_test1/geth/chaindata/ancient/chain readonly=false
INFO [01-06|15:43:36.528] Writing custom genesis block
INFO [01-06|15:43:36.530] Persisted trie from memory database      nodes=3 size=397.00B time="366.653µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [01-06|15:43:36.531] Successfully wrote genesis state         database=chaindata hash=0684f9..051ca9
INFO [01-06|15:43:36.531] Allocated cache and file handles         database=/Users/limingxie/blockchain/eth/data_test1/geth/lightchaindata cache=16.00MiB handles=16
INFO [01-06|15:43:36.629] Opened ancient database                  database=/Users/limingxie/blockchain/eth/data_test1/geth/lightchaindata/ancient/chain readonly=false
INFO [01-06|15:43:36.629] Writing custom genesis block
INFO [01-06|15:43:36.630] Persisted trie from memory database      nodes=3 size=397.00B time="288.531µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [01-06|15:43:36.631] Successfully wrote genesis state         database=lightchaindata hash=0684f9..051ca9
```

### 3.3 启动私链

```
geth --datadir /Users/limingxie/blockchain/eth/data_test1 --networkid 2222

INFO [12-15|11:25:18.126] Maximum peer count                       ETH=50 LES=0 total=50
INFO [12-15|11:25:18.131] Set global gas cap                       cap=50,000,000
INFO [12-15|11:25:18.133] Allocated trie memory caches             clean=154.00MiB dirty=256.00MiB
INFO [12-15|11:25:18.133] Allocated cache and file handles         database=/Users/limingxie/blockchain/eth/data_test1/geth/chaindata cache=512.00MiB handles=5120
INFO [12-15|11:25:18.188] Opened ancient database                  database=/Users/limingxie/blockchain/eth/data_test1/geth/chaindata/ancient/chain readonly=false
INFO [12-15|11:25:18.189]
INFO [12-15|11:25:18.189] ---------------------------------------------------------------------------------------------------------------------------------------------------------
INFO [12-15|11:25:18.189] Chain ID:  74631 (unknown)
... ... ...
... ... ...
INFO [12-15|11:25:18.192] Disk storage enabled for ethash caches   dir=/Users/limingxie/blockchain/eth/data_test1/geth/ethash count=3
INFO [12-15|11:25:18.192] Disk storage enabled for ethash DAGs     dir=/Users/limingxie/Library/Ethash count=2
INFO [12-15|11:25:18.192] Initialising Ethereum protocol           network=74631 dbversion=<nil>
... ... ...
INFO [12-15|11:25:18.195] Starting peer-to-peer node               instance=Geth/v1.10.26-stable-e5eb32ac/darwin-amd64/go1.18.1
INFO [12-15|11:25:18.234] New local node record                    seq=1,671,074,718,233 id=d089c43e9770701b ip=127.0.0.1 udp=30303 tcp=30303
INFO [12-15|11:25:18.234] Started P2P networking                   self=enode://dc671427a7cb8270e052b7a4a472165dc087f4abb04b91727ac59ff997ab0f649bbe5df319ac81130eaec8534f629594fe10d8b85014c7a459658af33a23232d@127.0.0.1:30303
INFO [12-15|11:25:18.236] IPC endpoint opened                      url=/Users/limingxie/blockchain/eth/data_test1/geth.ipc
INFO [12-15|11:25:18.236] Generated JWT secret                     path=/Users/limingxie/blockchain/eth/data_test1/geth/jwtsecret
INFO [12-15|11:25:23.239] WebSocket enabled                        url=ws://127.0.0.1:8551
INFO [12-15|11:25:23.239] HTTP server started                      endpoint=127.0.0.1:8551 auth=true prefix= cors=localhost vhosts=localhost
INFO [12-15|11:25:23.448] New local node record                    seq=1,671,074,718,234 id=d089c43e9770701b ip=115.171.57.62 udp=30303 tcp=30303
```

```bash
#创世模块
geth --datadir /Users/limingxie/blockchain/eth/data_test init /Users/limingxie/blockchain/eth/data_test/genesis.json

#启动
geth --identity "node0" --datadir "/Users/limingxie/blockchain/eth/data_test" --http --http.port 8545 --http.corsdomain="*" --http.addr "0.0.0.0" --http.api "eth,web3,miner,admin,personal,net"  --nodiscover --networkid 2222 --allow-insecure-unlock --port 2222 --ipcdisable 2>~/geth.log

#进入命令模式
geth attach http://127.0.0.1:8545

#查看账户余额
web3.fromWei(eth.getBalance(eth.accounts[0]),'ether');

#解锁用户
personal.unlockAccount(eth.accounts[0])
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
