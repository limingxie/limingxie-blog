---
author: "li_mingxie"
title: "【cosmos-wasm-笔记】部署智能合约"
date: 2023-09-25T23:28:49+08:00
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

简单的整理了智能合约的部署和使用。  
<!--more-->  

## 1.下载和编译测试合约代码

### 1.1 下载代码和编译
```bash
# 下载代码
$ git clone https://github.com/InterWasm/cw-contracts
$ cd cw-contracts
$ git checkout main
$ cd contracts/nameservice

# 使用稳定版编译
$ rustup default stable
$ cargo wasm

# 优化编译，可以让变异完的文件变小
$ RUSTFLAGS='-C link-arg=-s' cargo wasm
```

### 1.2 执行测试代码

```bash
$ RUST_BACKTRACE=1 cargo unit-test

running 15 tests
test tests::tests::proper_init_no_fees ... ok
test tests::tests::fails_on_register_insufficient_fees ... ok
test coin_helpers::test::assert_sent_sufficient_coin_works ... ok
test tests::tests::fails_on_register_wrong_fee_denom ... ok
test tests::tests::fails_on_register_already_taken_name ... ok
test tests::tests::fails_on_transfer_from_nonowner ... ok
test tests::tests::fails_on_transfer_insufficient_fees ... ok
test tests::tests::fails_on_transfer_non_existent ... ok
test tests::tests::proper_init_with_fees ... ok
test tests::tests::register_available_name_and_query_works ... ok
test tests::tests::register_available_name_fails_with_invalid_name ... ok
test tests::tests::returns_empty_on_query_unregistered_name ... ok
test tests::tests::register_available_name_and_query_works_with_fees ... ok
test tests::tests::transfer_works ... ok
test tests::tests::transfer_works_with_fees ... ok

test result: ok. 15 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out;
```

### 1.3 优化编译

可以更小 详情可以看这里： https://github.com/CosmWasm/rust-optimizer

```bash
$ docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.14.0
```

## 2.部署合约

### 2.1 存储合约

先查看list-code的信息

```bash
$ wasmd query wasm list-code $NODE
#或
$ wasmd query wasm list-code --node tcp://localhost:26657
```

存储bytecode以及获取CODE_ID

```bash
$ wasmd tx wasm store ./cw_nameservice.wasm --from validator $TXFLAG -y --output json --keyring-backend test
$ wasmd tx wasm store ./cw_nameservice.wasm --from validator --node tcp://localhost:26657 --chain-id test --gas-prices 0.0stake --gas auto --gas-adjustment 1.3 -y --output json --keyring-backend test

# 结果
gas estimate: 1530621
{"height":"0","txhash":"20335A347A77C1C5E63CEFA26BDF558419306CBACF100EBEA7A1E861825F9803","codespace":"","code":0,"data":"","raw_log":"[]","logs":[],"info":"","gas_wanted":"0","gas_used":"0","tx":null,"timestamp":"","events":[]}

$ RES=$(wasmd query tx 20335A347A77C1C5E63CEFA26BDF558419306CBACF100EBEA7A1E861825F9803 --output json)
$ CODE_ID=$(echo $RES | jq -r '.logs[0].events[-1].attributes[-1].value')
$ echo $CODE_ID
1
```

查看合约实例

```bash
$ wasmd query wasm list-contract-by-code $CODE_ID $NODE --output json
{"contracts":[],"pagination":{"next_key":null,"total":"0"}}
```

也可以下载合约文件，比对一下是不是一致

```bash
$ wasmd query wasm code $CODE_ID $NODE download.wasm
$ diff ./cw_nameservice.wasm ./download.wasm
```

### 2.2 创建合约实例

```bash
# 准备 instantiation 信息
$ INIT='{"purchase_price":{"amount":"100","denom":"stake"},"transfer_price":{"amount":"999","denom":"stake"}}'
# Instantiate 合约
$ wasmd tx wasm instantiate $CODE_ID "$INIT" --from validator --label "name service" $TXFLAG -y --no-admin --keyring-backend test

# 查看合约地址
$ wasmd query wasm list-contract-by-code $CODE_ID $NODE --output json
$ CONTRACT=$(wasmd query wasm list-contract-by-code $CODE_ID $NODE --output json | jq -r '.contracts[-1]')
$ echo $CONTRACT

# 查看合约信息
$ wasmd query wasm contract $CONTRACT $NODE
# 查看合约余额
$ wasmd query bank balances $CONTRACT $NODE

# 查看所有合约状态
$ wasmd query wasm contract-state all $CONTRACT $NODE
models:
- key: 636F6E666967
  value: eyJwdXJjaGFzZV9wcmljZSI6eyJkZW5vbSI6InN0YWtlIiwiYW1vdW50IjoiMTAwIn0sInRyYW5zZmVyX3ByaWNlIjp7ImRlbm9tIjoic3Rha2UiLCJhbW91bnQiOiI5OTkifX0=
pagination:
  next_key: null
  total: "0"
```

> 这结果的key值其实是config的16进制

```bash
$ echo 636F6E666967 | xxd -r -ps
config

$ echo -n config | xxd -ps
636f6e666967

$ wasmd query wasm contract-state all $CONTRACT $NODE --output "json" | jq -r '.models[0].key' | xxd -r -ps
config

$ wasmd query wasm contract-state all $CONTRACT $NODE --output "json" | jq -r '.models[0].value' | base64 -d
{"purchase_price":{"denom":"stake","amount":"100"},"transfer_price":{"denom":"stake","amount":"999"}}
```

也可以使用smart query查询

```bash
$ CONFIG_QUERY='{"config": {}}'
wasmd query wasm contract-state smart $CONTRACT "$CONFIG_QUERY" $NODE --output json
{"data":{"purchase_price":{"denom":"stake","amount":"100"},"transfer_price":{"denom":"stake","amount":"999"}}}
```

## 3. 调用合约

```bash
# 给账户地址登录一个名字叫 fred 
$ REGISTER='{"register":{"name":"fred"}}'
$ wasmd tx wasm execute $CONTRACT "$REGISTER" --amount 100stake --from validator $TXFLAG -y --keyring-backend test

# 使用 smart query 查询
$ NAME_QUERY='{"resolve_record": {"name": "fred"}}'
$ wasmd query wasm contract-state smart $CONTRACT "$NAME_QUERY" $NODE --output json
# {"data":{"address":"wasm1smfyqu0srwhmmzlsz8nk4uxhwdzdwwdazfwdqy"}}

$ wasmd keys add test --keyring-backend test
- address: wasm10hdygu8r5v79z6xdrpgma0afhk8szuvxee0v0j
  name: test
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AxDalYjk0aIEpuLWicdY/BGJRKf3/Hg8qDLRzLwQnye5"}'
  type: local

# 把所有权给到新添加的test用户
$ TRANSFER='{"transfer":{"name":"fred","to":"wasm10hdygu8r5v79z6xdrpgma0afhk8szuvxee0v0j"}}'
$ wasmd tx wasm execute $CONTRACT "$TRANSFER" --amount 999stake --from validator $TXFLAG -y --keyring-backend test

# 使用 smart query 查询变更后的结果
$ NAME_QUERY='{"resolve_record": {"name": "fred"}}'
$ wasmd query wasm contract-state smart $CONTRACT "$NAME_QUERY" $NODE --output json
# {"data":{"address":"wasm10hdygu8r5v79z6xdrpgma0afhk8szuvxee0v0j"}}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
