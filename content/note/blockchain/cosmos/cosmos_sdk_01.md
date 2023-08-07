---
author: "li_mingxie"
title: "【cosmos-sdk笔记】启动 simapp"
date: 2023-06-18T23:28:49+08:00
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

看看如何启用 cosmos-sdk 自带的测试用的 simapp。
<!--more-->  

## 1.克隆和编译代码

```shell
// 克隆代码
git clone https://github.com/cosmos/cosmos-sdk --depth=1 --branch v0.46.6
cd cosmos-sdk

// 编译代码
make  build
cd build

// 查看版本
./simd version
```

## 2.添加 key (账户)

```shell
./simd keys add alice
./simd keys add bob

// 为了方便操作，存到变量
export ALICE=$(./simd keys show alice --address)
export BOB=$(./simd keys show bob --address)
```

## 3.创建genesisfile

```shell
./simd init stone-age-1 --chain-id stone-age-block-chain
```

> 初始话以后，在/User/limingxie/.simapp/config 自动创建默认的genesis.json 文件  
> 把 stake 改成 自定义的 nstone  

```
addrbook.json
app.toml        <-- configuration for the app part of your blockchain
client.toml     <-- configuration for the CLI client of the app
config.toml     <-- configuration for Tendermint
genesis.json    <-- the genesis for your blockchain
gentx           <-- folder that contains the genesis transactions before they are inserted
node_key.json   <-- private key that uniquely identifies your node on the network
priv_validator_key.json
```

## 4.创建初始账户和验证者

```shell
// 给alice和bob 加nstone和nflint
./simd add-genesis-account alice 6000000000nstone,3000000000nflint
./simd add-genesis-account bob 5000000000nstone,2000000000nflint
```

```shell
// alice 质押 nstone 为做验证者做准备

./simd gentx alice \
    3000000000nstone \
    --account-number 0 --sequence 0 \
    --chain-id stone-age-block-chain \
    --pubkey '{"@type":"/cosmos.crypto.ed25519.PubKey","key":"A1GqoOS+Oxal9yfn/8cXO2KRQ470tWNx89JOKnbu53uq"}' \
    --gas 1000000 \
    --gas-prices 0.1nstone \
    --keyring-backend os
Genesis transaction written to "/Users/limingxie/.simapp/config/gentx/gentx-518a0d5c831631291be43590f66f7c521125770d.json"

// 把所有验证者关联起来
./simd collect-gentxs
```

## 5.打开节点

config.toml文件中找以下配置，可以修改node开放端口

```
[p2p]

# Address to listen for incoming connections
laddr = "tcp://0.0.0.0:26656"

... ...

external_address = "172.217.22.14:26656" # replace by your own
```

~/.simapp/config/node_key.json 存储着节点的私钥。  
config.toml文件位置在行中提到node_key_file = "config/node_key.json"。  
`node_key.json`丢失或被盗, 从新创建一个就行了，不会丢失任何代币。  
如下命令可以查看nodeId  

```shell
./simd tendermint show-node-id
e9b52965f638a1f0b40889fd56058d06b15f561b
```

## 6.和其它节点建立链接

开始的时候，在config.toml文件配置中手动保存 node-id@listen-address

```
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"

// 设为种子节点
seeds = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656"
```

> persistent_peers 里没必要把所有的节点写进去，你可以只写你信任的节点。

## 7.网络配置

~/.simd/config/config.toml 中可以设置  

调整节点通信情况  
max_num_inbound_peers  
handshake_timeout  

这些是你关系到你的节点性能  
db_backend  
log_level  

有些参数是和 BFT 相关  
timeout_prevote  
timeout_precommit_delta  

config/config.toml

```
[consensus]
...

timeout_propose = "3s"          # 提议超时时间：在收到提议块之前等待的时间长度，如果超过该时间，则进行预投票
timeout_propose_delta = "500ms" # 提议超时时间增量：每个轮次提议超时时间的增加量
timeout_prevote = "1s"          # 预投票超时时间：在收到超过2/3的预投票（"anything"，即不是单个块或nil）后等待的时间长度
timeout_prevote_delta = "500ms" # 预投票超时时间增量：每个轮次预投票超时时间的增加量。
timeout_precommit = "1s"        # 预提交超时时间：在收到超过2/3的预提交（"anything"，即不是单个块或nil）后等待的时间长度。
timeout_precommit_delta = "500ms" # 预提交超时时间增量：每个轮次预提交超时时间的增加量。
timeout_commit = "1s"           # 提交超时时间：在提交一个块后，在开始新的高度之前等待的时间长度（这给我们一个机会收到更多的预提交，即使我们已经有超过2/3的预提交）
```

查看tendermint版本

```shell
./simd tendermint version
ABCI: 0.17.0
BlockProtocol: 11
P2PProtocol: 8
Tendermint: v0.34.23
```

## 8.创建用户

```shell
sudo adduser chainuser

sudo mv ~/.myprojectd /home/chainuser
sudo chown -R chainuser:chainuser /home/chainuser/.simd
```

## 9.启动

```
./simd start
```

也可以作为服务启动  
保存如下文件： /etc/systemd/system/simd.service

```
[Unit]
Description=My Project Chain Daemon
After=network-online.target

[Service]
User=chainuser
ExecStart=$(which simd) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

启动服务

```
sudo systemctl daemon-reload
sudo systemctl enable simd

sudo systemctl start simd
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
