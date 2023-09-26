---
author: "li_mingxie"
title: "【cosmos-wasm-笔记】基本概念以及环境配置"
date: 2023-09-22T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "cosmos",
    "wasm",
]
categories: [
    "blockchain",
    "cosmos",
]
---

这里开始整理一下cosmos生态的智能合约。  
cosmos生态中智能合约是通过cosmos wasm实现的。
<!--more-->  

## 1.结构简介

第一次接触cosmos wasm 时候以为这也是一个服务，  
除使用cosmos-sdk自建的服务之外，还需要启动wasm服务呢。  
后续才知道这也其实是一个框架，在你的自建服务里只需引用的wasm的引用包，  
实现功能就可以了，因为cosmos-sdk提供simapp，wams提供wasmd示例。  
很容易误认为用智能合约，需要启动这两个服务。  

simapp和wasmd其实都是使用cosmos-sdk架构搭建的例子。  
simapp侧重的是cosmos-sdk架构本身的功能，  
wasmd侧重的是cosmos-sdk架构的基础上多引用了wasm架构的智能合约功能。  

所以了解wasm时候直接clone wasm的 github项目即可。  
wasmd 包含链和共识功能。  

## 2.环境配置

### 2.1 安装rust
首先需要安装rust。因为wasm智能合约编写的首选语言是rust。  
可以百度一下，或参看这里。 https://rustup.rs/  

### 2.2 下载编译wasmd代码

```bash
$ git clone https://github.com/CosmWasm/wasmd.git
$ cd wasmd
# 来去版本标签
$ git fetch --tags
# 切换到最新的版本
$ git checkout v0.41.0
$ make install

# 查看版本是否按转成功
$ wasmd version
```

### 2.3 启动wasmd链

简单的编写一个脚本。

```sh
#!/bin/bash
rm -rf ~/.wasmd

export DAEMON_NAME=wasmd
export DAEMON_HOME=$HOME/.wasmd
export DAEMON_RESTART_AFTER_UPGRADE=true

./wasmd init test --chain-id test --overwrite

cat <<< $(jq '.app_state.gov.voting_params.voting_period = "20s"' $HOME/.wasmd/config/genesis.json) > $HOME/.wasmd/config/genesis.json
cat <<< $(jq '.app_state.gov.deposit_params.max_deposit_period = "30s"' $HOME/.wasmd/config/genesis.json) > $HOME/.wasmd/config/genesis.json

./wasmd keys add validator --keyring-backend test
./wasmd genesis add-genesis-account validator 1000000000000000000000stake --keyring-backend test 
./wasmd genesis gentx validator 1000000stake --chain-id test  --keyring-backend test 
./wasmd genesis collect-gentxs 

mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
cp ./wasmd $DAEMON_HOME/cosmovisor/genesis/bin

cosmovisor run start
```

添加执行权限以及启动本地链

```bash
$ chmod +x start.sh
$ ./start.sh
```

## 3. 验证和变量设置

查看账户

```bash
$ wasmd keys list --keyring-backend test
- address: wasm18gf6sqfah5qp9gxf0qc3srkwe873kazv34z8x3
  name: validator
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AlfQionhph5z5CSF1BoHk/Yt2eUalOf4s72wOlEqhubY"}'
  type: local

$ wasmd query bank balances wasm18gf6sqfah5qp9gxf0qc3srkwe873kazv34z8x3
balances:
- amount: "999999999999998617344"
  denom: stake
pagination:
  next_key: null
  total: "0"
```

设置好变量，后续章节使用

```bash
$ export NODE=(--node tcp://localhost:26657)
$ export TXFLAG=($NODE --chain-id test --gas-prices 0.25stake --gas auto --gas-adjustment 1.3 )
```

下一遍整理如何编译合约以及发布合约。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
