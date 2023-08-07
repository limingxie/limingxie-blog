---
author: "li_mingxie"
title: "【cosmos-sdk-多节点笔记】加入出块验证者以及备份"
date: 2023-08-06T23:28:49+08:00
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

根据上一篇文章先启用多节点的私链后，加入验证者以及做备份。
<!--more-->  

## 1.加入出块验证

查看目前的质押情况

```bash
$ ./mingxied query staking validators --home="/root/.mingxiedir1"
pagination:
  next_key: null
  total: "0"
validators:
- commission:
    commission_rates:
      max_change_rate: "0.010000000000000000"
      max_rate: "0.200000000000000000"
      rate: "0.100000000000000000"
    update_time: "2023-08-07T06:16:53.431683Z"
  consensus_pubkey:
    '@type': /cosmos.crypto.ed25519.PubKey
    key: MTpIZw8qQq56phwriW+eiEjaTNwfIotXNtsALdoP+cU=
  delegator_shares: "51000000.000000000000000000"
  description:
    details: ""
    identity: ""
    moniker: node1
    security_contact: ""
    website: ""
  jailed: false
  min_self_delegation: "1"
  operator_address: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
  status: BOND_STATUS_BONDED
  tokens: "51000000"
  unbonding_height: "0"
  unbonding_time: "1970-01-01T00:00:00Z"
```

在第二个节点，创建新的出块验证者

```bash
# 创建验证者
./mingxied tx staking create-validator \
  --amount=41000000stake \
  --commission-max-change-rate=0.1 \
  --commission-max-rate=0.2 \
  --commission-rate=0.1 \
  --details="details" \
  --min-self-delegation=1 \
  --pubkey=$(./mingxied tendermint show-validator --home="/root/.mingxiedir2/") \
  --from=guanyu \
  --chain-id=taoyuan \
  --home="/root/.mingxiedir2"


# 执行结果
auth_info:
  fee:
    amount: []
    gas_limit: "200000"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /cosmos.staking.v1beta1.MsgCreateValidator
    commission:
      max_change_rate: "0.100000000000000000"
      max_rate: "0.200000000000000000"
      rate: "0.100000000000000000"
    delegator_address: cosmos19e4v63424tyjz94vkmq3xlpe3yq4p5qxvrfj0q
    description:
      details: details
      identity: ""
      moniker: node2
      security_contact: ""
      website: ""
    min_self_delegation: "1"
    pubkey:
      '@type': /cosmos.crypto.ed25519.PubKey
      key: m6+C7BBhZZBF2m2ArjIMbNvbm7lhujh3cdLKB4gPRWI=
    validator_address: cosmosvaloper19e4v63424tyjz94vkmq3xlpe3yq4p5qxfha8rn
    value:
      amount: "41000000"
      denom: stake
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]: y
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: 1973BF748F7BE6989034E1CC66D6B3B06AA8CF99FDCC0413A413A63D6794266F
```

再次查看出块验证者

```bash
./mingxied query staking validators --home="/root/.mingxiedir1"
pagination:
  next_key: null
  total: "0"
validators:
- commission:
    commission_rates:
      max_change_rate: "0.100000000000000000"
      max_rate: "0.200000000000000000"
      rate: "0.100000000000000000"
    update_time: "2023-08-07T06:41:45.410712Z"
  consensus_pubkey:
    '@type': /cosmos.crypto.ed25519.PubKey
    key: m6+C7BBhZZBF2m2ArjIMbNvbm7lhujh3cdLKB4gPRWI=
  delegator_shares: "41000000.000000000000000000"
  description:
    details: details
    identity: ""
    moniker: node2
    security_contact: ""
    website: ""
  jailed: false
  min_self_delegation: "1"
  operator_address: cosmosvaloper19e4v63424tyjz94vkmq3xlpe3yq4p5qxfha8rn
  status: BOND_STATUS_BONDED
  tokens: "41000000"
  unbonding_height: "0"
  unbonding_time: "1970-01-01T00:00:00Z"
- commission:
    commission_rates:
      max_change_rate: "0.010000000000000000"
      max_rate: "0.200000000000000000"
      rate: "0.100000000000000000"
    update_time: "2023-08-07T06:16:53.431683Z"
  consensus_pubkey:
    '@type': /cosmos.crypto.ed25519.PubKey
    key: MTpIZw8qQq56phwriW+eiEjaTNwfIotXNtsALdoP+cU=
  delegator_shares: "51000000.000000000000000000"
  description:
    details: ""
    identity: ""
    moniker: node1
    security_contact: ""
    website: ""
  jailed: false
  min_self_delegation: "1"
  operator_address: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
  status: BOND_STATUS_BONDED
  tokens: "51000000"
  unbonding_height: "0"
  unbonding_time: "1970-01-01T00:00:00Z"
```

## 2.获取出块奖励

### 2.1 查看奖励

查看目前的奖励  
./mingxied query distribution rewards [delegator-addr] [validator-addr]

```bash
# 查看账户信息
./mingxied keys list --keyring-backend os --home="/root/.mingxiedir1"
- address: cosmos19e4v63424tyjz94vkmq3xlpe3yq4p5qxvrfj0q
  name: guanyu
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"At6kLGqnnRBchPVxnZki2ZeC/BYPVmMeY8cwhv1Xsn1p"}'
  type: local
- address: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
  name: liubei
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AmWsbCNr/R1uRX1NUMu4iUov7nP7+N7lbAif4+nuUXEG"}'
  type: local

# 查看 liubei账户的奖励
./mingxied query distribution rewards cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
rewards:
- reward:
  - amount: "4722703550.722467390255000000"
    denom: stake
  validator_address: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
total:
- amount: "4722703550.722467390255000000"
  denom: stake

# liubei账户资产
./mingxied query bank balances cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
balances:
- amount: "949999949000000"
  denom: stake
pagination:
  next_key: null
  total: "0"
```

### 2.2 申请领取奖励

```bash
# 领取奖励申请
./mingxied tx distribution withdraw-rewards cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx --from liubei --chain-id taoyuan --keyring-backend os --home="/Users/limingxie/.mingxiedir1"
auth_info:
  fee:
    amount: []
    gas_limit: "200000"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward
    delegator_address: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
    validator_address: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]: y
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: CA693C75322E6514A6D01FA4965659F26E54F6FCB3422B7C8E31A9698CDD66C9

# 查看执行结果
./mingxied query tx CA693C75322E6514A6D01FA4965659F26E54F6FCB3422B7C8E31A9698CDD66C9 --home="/Users/limingxie/.mingxiedir1"
code: 0
codespace: ""
data: 12580A3F2F636F736D6F732E646973747269627574696F6E2E763162657461312E4D7367576974686472617744656C656761746F72526577617264526573706F6E736512150A130A057374616B65120A36383631313139383739
events:
- attributes:
  - index: true
    key: ZmVl
    value: null
  - index: true
    key: ZmVlX3BheWVy
    value: Y29zbW9zMWVzMjA2d3dkaDNqNWM1djB6NDJ5bWMzM3JkbGg3bTRmbmYzMjA0
  type: tx
- attributes:
  - index: true
    key: YWNjX3NlcQ==
    value: Y29zbW9zMWVzMjA2d3dkaDNqNWM1djB6NDJ5bWMzM3JkbGg3bTRmbmYzMjA0LzE=
  type: tx
- attributes:
  - index: true
    key: c2lnbmF0dXJl
    value: a1Fvd1poK0tobitPd01KdG1Pelp3cVkrNnBCQXhvN1RyaVhxRHVHaWdVdDR6SXRWbzdwckxpZ3N6WjBSM0NwS2lxMVhLWlVkQlIzWUtIN0FTQkxCeHc9PQ==
  type: tx
- attributes:
  - index: true
    key: YWN0aW9u
    value: L2Nvc21vcy5kaXN0cmlidXRpb24udjFiZXRhMS5Nc2dXaXRoZHJhd0RlbGVnYXRvclJld2FyZA==
  type: message
- attributes:
  - index: true
    key: c3BlbmRlcg==
    value: Y29zbW9zMWp2NjVzM2dycWY2djZqbDNkcDR0NmM5dDlyazk5Y2Q4OGx5dWZs
  - index: true
    key: YW1vdW50
    value: Njg2MTExOTg3OXN0YWtl
  type: coin_spent
- attributes:
  - index: true
    key: cmVjZWl2ZXI=
    value: Y29zbW9zMWVzMjA2d3dkaDNqNWM1djB6NDJ5bWMzM3JkbGg3bTRmbmYzMjA0
  - index: true
    key: YW1vdW50
    value: Njg2MTExOTg3OXN0YWtl
  type: coin_received
- attributes:
  - index: true
    key: cmVjaXBpZW50
    value: Y29zbW9zMWVzMjA2d3dkaDNqNWM1djB6NDJ5bWMzM3JkbGg3bTRmbmYzMjA0
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMWp2NjVzM2dycWY2djZqbDNkcDR0NmM5dDlyazk5Y2Q4OGx5dWZs
  - index: true
    key: YW1vdW50
    value: Njg2MTExOTg3OXN0YWtl
  type: transfer
- attributes:
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMWp2NjVzM2dycWY2djZqbDNkcDR0NmM5dDlyazk5Y2Q4OGx5dWZs
  type: message
- attributes:
  - index: true
    key: YW1vdW50
    value: Njg2MTExOTg3OXN0YWtl
  - index: true
    key: dmFsaWRhdG9y
    value: Y29zbW9zdmFsb3BlcjFlczIwNnd3ZGgzajVjNXYwejQyeW1jMzNyZGxoN200ZmthOWxyeA==
  type: withdraw_rewards
- attributes:
  - index: true
    key: bW9kdWxl
    value: ZGlzdHJpYnV0aW9u
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMWVzMjA2d3dkaDNqNWM1djB6NDJ5bWMzM3JkbGg3bTRmbmYzMjA0
  type: message
gas_used: "106371"
gas_wanted: "200000"
height: "282"
info: ""
logs:
- events:
  - attributes:
    - key: receiver
      value: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
    - key: amount
      value: 6861119879stake
    type: coin_received
  - attributes:
    - key: spender
      value: cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl
    - key: amount
      value: 6861119879stake
    type: coin_spent
  - attributes:
    - key: action
      value: /cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward
    - key: sender
      value: cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl
    - key: module
      value: distribution
    - key: sender
      value: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
    type: message
  - attributes:
    - key: recipient
      value: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
    - key: sender
      value: cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl
    - key: amount
      value: 6861119879stake
    type: transfer
  - attributes:
    - key: amount
      value: 6861119879stake
    - key: validator
      value: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
    type: withdraw_rewards
  log: ""
  msg_index: 0
raw_log: '[{"msg_index":0,"events":[{"type":"coin_received","attributes":[{"key":"receiver","value":"cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204"},{"key":"amount","value":"6861119879stake"}]},{"type":"coin_spent","attributes":[{"key":"spender","value":"cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl"},{"key":"amount","value":"6861119879stake"}]},{"type":"message","attributes":[{"key":"action","value":"/cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward"},{"key":"sender","value":"cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl"},{"key":"module","value":"distribution"},{"key":"sender","value":"cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204"}]},{"type":"transfer","attributes":[{"key":"recipient","value":"cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204"},{"key":"sender","value":"cosmos1jv65s3grqf6v6jl3dp4t6c9t9rk99cd88lyufl"},{"key":"amount","value":"6861119879stake"}]},{"type":"withdraw_rewards","attributes":[{"key":"amount","value":"6861119879stake"},{"key":"validator","value":"cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx"}]}]}]'
timestamp: "2023-08-07T06:56:18Z"
tx:
  '@type': /cosmos.tx.v1beta1.Tx
  auth_info:
    fee:
      amount: []
      gas_limit: "200000"
      granter: ""
      payer: ""
    signer_infos:
    - mode_info:
        single:
          mode: SIGN_MODE_DIRECT
      public_key:
        '@type': /cosmos.crypto.secp256k1.PubKey
        key: AmWsbCNr/R1uRX1NUMu4iUov7nP7+N7lbAif4+nuUXEG
      sequence: "1"
    tip: null
  body:
    extension_options: []
    memo: ""
    messages:
    - '@type': /cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward
      delegator_address: cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204
      validator_address: cosmosvaloper1es206wwdh3j5c5v0z42ymc33rdlh7m4fka9lrx
    non_critical_extension_options: []
    timeout_height: "0"
  signatures:
  - kQowZh+Khn+OwMJtmOzZwqY+6pBAxo7TriXqDuGigUt4zItVo7prLigszZ0R3CpKiq1XKZUdBR3YKH7ASBLBxw==
txhash: CA693C75322E6514A6D01FA4965659F26E54F6FCB3422B7C8E31A9698CDD66C9
```

```bash
# 查看 liubei 账户的余额
./mingxied query bank balances cosmos1es206wwdh3j5c5v0z42ymc33rdlh7m4fnf3204 --home="/Users/limingxie/.mingxiedir1"
balances:
- amount: "950006810119879"
  denom: stake
pagination:
  next_key: null
  total: "0"

## 如果报 failed to execute message; message index: 0: no validator distribution info
## 需要先执行一次 withdraw-all-rewards 之后再用 withdraw-rewards 申请获取奖励
```

### 2.3 委托管理

如果需要把自己的币委托给别的账户，可以用<appd> tx staking delegate命令

```bash
<appd> tx staking delegate cosmosvaloper1l2rsakp388kuv9k8qzq6lrm9taddae7fpx59wm 1000stake --from mykey
```

## 3.状态快照以及备份

```bash
# 快照，直接替换 原来的 genesis.json 后启动链
./mingxied export --for-zero-height --height=<height> > genesis-<height>.json
```

### 备份

直接备份 --home 的目录文件。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
