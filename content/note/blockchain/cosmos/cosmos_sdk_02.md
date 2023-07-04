---
author: "li_mingxie"
title: "【cosmos-sdk笔记】x-authz module"
date: 2023-05-19T23:28:49+08:00
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

认证auth(authentication)  
授权authz(authorization)  

<!--more-->  

## 1.配置启动链

simd 版本是 v0.44.0

```shell
./simd config chain-id demo
./simd config keyring-backend test

// 添加用户
./simd keys add alice --recover
> Enter your bip39 mnemonic
plunge hundred health electric victory foil marine elite shiver tonight away verify vacuum giant pencil ocean nest pledge okay endless try spirit special start

./simd keys add bob --recover
shuffle oppose diagram wire rubber apart blame entire thought firm carry swim old head police panther lyrics road must silly sting dirt hard organ


./simd init test --chain-id demo
./simd add-genesis-account alice 5000000000stake --keyring-backend test
./simd add-genesis-account bob 5000000000stake --keyring-backend test
./simd gentx alice 1000000stake --chain-id demo
./simd collect-gentxs

// 启动
./simd start
```

## 2.提交治理提案

```shell
./simd tx gov submit-proposal --title="Test Authorization" --description="Is Bob authorized to vote?" --type="Text" --deposit="10000000stake" --from alice

// 执行结果
{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.gov.v1beta1.MsgSubmitProposal",
                "content": {
                    "@type": "/cosmos.gov.v1beta1.TextProposal",
                    "title": "Test Authorization",
                    "description": "Is Bob authorized to vote?"
                },
                "initial_deposit": [
                    {
                        "denom": "stake",
                        "amount": "10000000"
                    }
                ],
                "proposer": "cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh"
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
txhash: 7E2503BB0BE5ACFB527D03E64BFA562688BFB5146DF1D1F06DBE0D4F5740B5AD

// 查看提案内容 
./simd query gov proposal 1

// 结果
content:
  '@type': /cosmos.gov.v1beta1.TextProposal
  description: Is Bob authorized to vote?
  title: Test Authorization
deposit_end_time: "2023-07-05T05:28:57.987931Z"
final_tally_result:
  abstain: "0"
  "no": "0"
  no_with_veto: "0"
  "yes": "0"
proposal_id: "1"
status: PROPOSAL_STATUS_VOTING_PERIOD
submit_time: "2023-07-03T05:28:57.987931Z"
total_deposit:
- amount: "10000000"
  denom: stake
voting_end_time: "2023-07-05T05:28:57.987931Z"
voting_start_time: "2023-07-03T05:28:57.987931Z"

```

## 3.Create authorization

```shell
// 授权给 bob
./simd tx authz grant cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt generic --msg-type /cosmos.gov.v1beta1.MsgVote --from alice

// 执行结果
{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.authz.v1beta1.MsgGrant",
                "granter": "cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh",
                "grantee": "cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt",
                "grant": {
                    "authorization": {
                        "@type": "/cosmos.authz.v1beta1.GenericAuthorization",
                        "msg": "/cosmos.gov.v1beta1.MsgVote"
                    },
                    "expiration": "2024-07-03T07:00:53Z"
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
txhash: 6D8B8D519396E8B776667D028E0E68954BCFA0734D6E2E24E284238CD893384B
```

```shell
./simd query authz grants cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt /cosmos.gov.v1beta1.MsgVote
grants:
- authorization:
    '@type': /cosmos.authz.v1beta1.GenericAuthorization
    msg: /cosmos.gov.v1beta1.MsgVote
  expiration: "2024-07-03T07:00:53Z"
pagination: null
```

## 4.创建一个未签名的交易

```shell
 ./simd tx gov vote 1 yes --from cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh --generate-only > tx.json
 ```

执行交易

```shell
./$ simd tx authz exec tx.json --from bob

{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.authz.v1beta1.MsgExec",
                "grantee": "cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt",
                "msgs": [
                    {
                        "@type": "/cosmos.gov.v1beta1.MsgVote",
                        "proposal_id": "1",
                        "voter": "cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh",
                        "option": "VOTE_OPTION_YES"
                    }
                ]
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
txhash: AF1319F8550B53F003C1A3098A8482F572925C57723ADF6929983C96043EA811
```

```shell
./simd query gov vote 1 cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh
option: VOTE_OPTION_YES
options:
- option: VOTE_OPTION_YES
  weight: "1.000000000000000000"
proposal_id: "1"
voter: cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh
```

## 5.撤销交易

```shell
./simd tx authz revoke cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt /cosmos.gov.v1beta1.MsgVote --from alice

{
    "body": {
        "messages": [
            {
                "@type": "/cosmos.authz.v1beta1.MsgRevoke",
                "granter": "cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh",
                "grantee": "cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt",
                "msg_type_url": "/cosmos.gov.v1beta1.MsgVote"
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
txhash: B18B4BCEBE3F327E540F7DEE7F4B5D87EF22E8468C5F0D546F6E464E9EC2CE8B
```

// 查看授权

```shell
./simd query authz grants cosmos1jxd2uhx0j6e59306jq3jfqs7rhs7cnhvey4lqh cosmos1khljzagdncfs03x5g6rf9qp5p93z9qgc3w5dwt /cosmos.gov.v1beta1.MsgVote
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
