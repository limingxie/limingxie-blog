---
author: "li_mingxie"
title: "【cosmos-sdk笔记】x-feegrant module"
date: 2023-05-20T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "cosmos",
]
categories: [
    "blockchain",
    "eth",
    "solidity",
]
---

BasicAllowance  
该津贴有一次性限制、过期或无限制。  
PeriodicAllowance  
该津贴有限额，该限额会定期重置。  
<!--more-->  

## 1. 准备环境以及启动链

simd 版本是 v0.44.0

```shell
./simd config chain-id demo
./simd config keyring-backend test

// 添加用户
./simd keys add alice
./simd keys add bob


// 为了方便写到变量
export ALICE=$(./simd keys show alice --address)
export BOB=$(./simd keys show bob --address)

// 初始化链
./simd init test --chain-id demo
./simd add-genesis-account alice 5000000000stake --keyring-backend test
./simd add-genesis-account bob 2000kudos --keyring-backend test
./simd gentx alice 1000000stake --chain-id demo
./simd collect-gentxs

// 启动
./simd start
```

## 2. feegrant grant

```
simd tx feegrant grant [granter_key_or_address] [grantee]  
--from string         Name or address of private key with which to sign  
--spend-limit string  Spend limit specifies the max limit can be used, if not mentioned there is no limit  
```

```shell
./simd tx feegrant grant $ALICE $BOB --from alice --spend-limit 100000stake
{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.feegrant.v1beta1.MsgGrantAllowance",
                "granter": "cosmos14cvn7rl270t0w3y9hgrnxgmja903dw29syyqej",
                "grantee": "cosmos195gar5ada3nlxzfu23smmq0p5waagt9yg4q2k7",
                "allowance": {
                    "@type": "/cosmos.feegrant.v1beta1.BasicAllowance",
                    "spend_limit": [
                        {
                            "denom": "stake",
                            "amount": "100000"
                        }
                    ],
                    "expiration": null
                }
            }
        ],
        "memo": "",
        "timeout_height": "0",
        "extension_options": [],
        "non_critical_extension_options": []
    },
    "auth_info": {
        "signer_infos": [],
        "fee": {
            "amount": [],
            "gas_limit": "200000",
            "payer": "",
            "granter": ""
        }
    },
    "signatures": []
}

confirm transaction before signing and broadcasting [y/N]: y
code: 0
codespace: ""
data: ""
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: 04B12BD58966A30AC64E060FD879ECC092AC20614D10E2C769C4035DE5D3640C
```

```shell
./simd query feegrant grants $BOB

allowances:
- allowance:
    '@type': /cosmos.feegrant.v1beta1.BasicAllowance
    expiration: null
    spend_limit:
    - amount: "100000"
      denom: stake
  grantee: cosmos195gar5ada3nlxzfu23smmq0p5waagt9yg4q2k7  //bob
  granter: cosmos14cvn7rl270t0w3y9hgrnxgmja903dw29syyqej  //alice
pagination:
  next_key: null
  total: "0"
```

```shell
./simd query bank balances $ALICE
./simd query bank balances $BOB
```

ALICE的钱少了100000stake是因为添加到 Bob 签署的交易中

```shell
./simd query feegrant grants $BOB

allowances:
- allowance:
    '@type': /cosmos.feegrant.v1beta1.BasicAllowance
    expiration: null
    spend_limit:
    - amount: "100000"
      denom: stake
  grantee: cosmos195gar5ada3nlxzfu23smmq0p5waagt9yg4q2k7  //bob
  granter: cosmos14cvn7rl270t0w3y9hgrnxgmja903dw29syyqej  //alice
pagination:
  next_key: null
  total: "0"
```

## 3. 撤销

```shell
./simd tx feegrant revoke $ALICE $BOB --from alice
{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.feegrant.v1beta1.MsgRevokeAllowance",
                "granter": "cosmos14cvn7rl270t0w3y9hgrnxgmja903dw29syyqej",
                "grantee": "cosmos195gar5ada3nlxzfu23smmq0p5waagt9yg4q2k7"
            }
        ],
        "memo": "",
        "timeout_height": "0",
        "extension_options": [],
        "non_critical_extension_options": []
    },
    "auth_info": {
        "signer_infos": [],
        "fee": {
            "amount": [],
            "gas_limit": "200000",
            "payer": "",
            "granter": ""
        }
    },
    "signatures": []
}

confirm transaction before signing and broadcasting [y/N]: y
code: 0
codespace: ""
data: ""
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: 02C9CCDDB531B86873677A5E8664C92191CAE0A2A81B5ABCEF9EF4F08BD3DAF7

// 再次查看
./simd query feegrant grants $BOB
allowances: []
pagination:
  next_key: null
  total: "0"
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
