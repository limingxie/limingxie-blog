---
author: "li_mingxie"
title: "【cosmos-sdk笔记】启动多节点"
date: 2023-08-01T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "cosmos",
]
categories: [
    "blockchain",
    "cosmos",
]
---

测试了多节点的启用，做了操作笔记，供以后参考。
<!--more-->  

## 1.创建项目

首先引用cosmos-sdk简单的写了一个 tvote的模块。  

```
git clone https://github.com/taoyuans/mingxie.git

cd mingxie
git checkout main_v1

go build -o ./build/mingxied ./cmd/mingxied/main.go
cd build
```

## 2.查看帮助

是否有 tvote 模块

```bash
$ ./mingxied tx tvote -h
tvote transactions subcommands

Usage:
  mingxied tx tvote [flags]
  mingxied tx tvote [command]

Available Commands:
  save-voter  Broadcast message save-voter

Flags:
  -h, --help   help for tvote

Global Flags:
      --chain-id string     The network chain ID (default "mingxie")
      --home string         directory for config and data (default "/Users/limingxie/.mingxie")
      --log_format string   The logging format (json|plain) (default "plain")
      --log_level string    The logging level (trace|debug|info|warn|error|fatal|panic) (default "info")
      --trace               print out full stack trace on errors
```

## 3.初始化配置

```bash
# 节点 1
./mingxied config chain-id taoyuan --home="/root/.mingxiedir1"
./mingxied config keyring-backend os --home="/root/.mingxiedir1"
./mingxied keys add liubei --keyring-backend os --home="/root/.mingxiedir1"
./mingxied keys add guanyu --keyring-backend os --home="/root/.mingxiedir1"

./mingxied init node1 --chain-id taoyuan --home="/root/.mingxiedir1"

./mingxied add-genesis-account liubei 950000000000000stake --keyring-backend os --home="/root/.mingxiedir1"
./mingxied add-genesis-account guanyu 850000000000000stake --keyring-backend os --home="/root/.mingxiedir1"

./mingxied gentx liubei 51000000stake --chain-id taoyuan --moniker="node1" --home="/root/.mingxiedir1"
./mingxied collect-gentxs

# 节点 2
./mingxied config chain-id taoyuan --home="/root/.mingxiedir2"
./mingxied config keyring-backend os --home="/root/.mingxiedir2"
cp /root/.mingxiedir1/guanyu.info /root/.mingxiedir2/
cp /root/.mingxiedir1/config/genesis.json /root/.mingxiedir2/config/
```

## 4.节点之间的链接

```bash
./mingxied tendermint show-node-id --home="/root/.mingxiedir1"
f15e48b8162b9f20c59efa384fe9f85501c30e06

./mingxied tendermint show-node-id --home="/root/.mingxiedir2"
d931f08afe4b821a4bc21b6246f8a3a3cd54eefd
```

根据实际情况修改  
config/confing.toml的persistent_peers  

```bash
vi /root/.mingxiedir1/config/confing.toml

# Comma separated list of nodes to keep persistent connections to
persistent_peers = "f15e48b8162b9f20c59efa384fe9f85501c30e06@192.168.1.1:26656,9dc322a1d7420d1253ffe4d4b375e392cf696393@192.168.2:26656"

#如果是在同一台机器起两个节点，第二个节点需要修改端口配置
vi /root/.mingxiedir2/config/confing.toml
vi /root/.mingxiedir2/config/app.toml
```

## 5.启动连

```bash
./mingxied start --home="/root/.mingxiedir1"
./mingxied start --home="/root/.mingxiedir2"
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
