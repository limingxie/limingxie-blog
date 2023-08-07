---
author: "li_mingxie"
title: "【cosmos-sdk笔记】x-gov module"
date: 2023-07-10T23:28:49+08:00
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

GOV治理。它允许创建任何消息类型的提案，并对它们进行投票。
<!--more-->  

## 1. Gov几个概念

simd 版本是 v0.46.6

### 提案Proposal  

提案是提交投票建议到网络上。提案提交后会有唯一的提案ID。  

### 消息Message

提案包含sdk.Msg数组，如果提案通过，就会自动执行。  
这意味着您可以提交关于治理模块有权执行的任何建议。  

### 存款期Deposit period

为了过滤无效信息，提交建议时必须先存入一定的coin到链中。  
例如Cosmos Hub需要64ATOM押金。押金是在投票后会退还给存款人。  
提案被否决：在这种情况下，存款将被烧毁。

> 提议人没有义务提交存款总额。其他用户也可以缴纳押金。

### 投票期Voting period

满足最低保证金后，提案进入投票期。  
在此期间，用户可以对提案进行投票。投票周期是各个链的一个参数。  
例如，Cosmos Hub有一个2weeks投票期。  

### 投票选项Voting options

投票者可以在Yes, No, NoWithVeto, Abstain.  
NoWithVeto允许选民投反对票，也可以否决提案，如果提案否决，会自动的销毁deposit。  
Abstain允许选民放弃投票。Abstain与不投票不同，因为投票有助于达到法定人数。  

### 投票权重Voting weight

每张选票根据选民所抵押的代币量为加权值。这是投票结束时的deposit coin的数量来确定加权值。  
和投票开始和投票中的deposit coin数量是没关系。  

### 法定人数Quorum

法定人数定义为提案所需投票权的最低百分比，结果才有效。如果未达到法定人数，该提案将被拒绝。

> 在Cosmos网络中，当一个参与者发起一个提案proposal，  
他们需要提供一定数量的代币作为押金(deposit)  
其目的是防止滥用提案系统。  

> 当提案通过时deposit会退还给存款人。  
但是提案被拒绝，提案未通过时押金deposit会被销毁。

## 2.配置环境

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

// 默认投票周期为172800秒(两天)。更改为180s(三分钟)。
cat <<< $(jq '.app_state.gov.voting_params.voting_period = "180s"' ~/.simapp/config/genesis.json) > ~/.simapp/config/genesis.json

./simd add-genesis-account alice 5000000000stake --keyring-backend test
./simd add-genesis-account bob 5000000000stake --keyring-backend test
./simd gentx alice 1000000stake --chain-id demo
./simd collect-gentxs

// 启动
./simd start
```

## 3.Create a proposal

```shell
./simd tx gov draft-proposal

✔ text
Enter proposal title: Test Proposal
Enter proposal authors: Alice
Enter proposal summary: A test proposal with simapp
Enter proposal details: -
Enter proposal proposal forum url: https://example.org/proposal/1
Enter proposal vote option context: YES: XX, NO: YX, ABSTAIN: XY, NO_WITH_VETO: YY
Enter proposal deposit: 10stake
Your draft proposal has successfully been generated.
Proposals should contain off-chain metadata, please upload the metadata JSON to IPFS.
Then, replace the generated metadata field with the IPFS CID.

$ ls
draft_metadata.json draft_proposal.json
```

创建完成后，会创建2个json文件。

## 4.配置IPFS

```
$ rm -rf /Users/limingxie/.ipfs
$ ipfs init
$ ipfs add draft_metadata.json
added QmX9uKdmz2YdRw3yNtU2WPf1d2ErPYz7Qegq2ggoo3wj89 draft_metadata.json
$ ipfs daemon

vi draft_proposal.json
//"metadata": "ipfs://QmX9uKdmz2YdRw3yNtU2WPf1d2ErPYz7Qegq2ggoo3wj89",
```

## 5.提交提案

```shell
./simd tx gov submit-proposal draft_proposal.json --from alice --keyring-backend test
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
  - '@type': /cosmos.gov.v1.MsgSubmitProposal
    initial_deposit:
    - amount: "10"
      denom: stake
    messages: []
    metadata: ipfs://QmX9uKdmz2YdRw3yNtU2WPf1d2ErPYz7Qegq2ggoo3wj89
    proposer: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
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
txhash: A97AAC7744E43383E8FC5F27DBFD112DCB2079EC31490B91DCE9A19FF948F183
```

```shell
./simd query tx A97AAC7744E43383E8FC5F27DBFD112DCB2079EC31490B91DCE9A19FF948F183
code: 0
codespace: ""
data: 122E0A282F636F736D6F732E676F762E76312E4D73675375626D697450726F706F73616C526573706F6E736512020801
events:
- attributes:
  - index: true
    key: ZmVl
    value: null
  - index: true
    key: ZmVlX3BheWVy
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1o
  type: tx
- attributes:
  - index: true
    key: YWNjX3NlcQ==
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1oLzE=
  type: tx
- attributes:
  - index: true
    key: c2lnbmF0dXJl
    value: R2xGcnVWS2ZLdVBNODd3T0M2YWU1azRpZHFkcmRSY2FpV2hPemNYWDh5RXY4cjYrOEF2WG41MEszLzZramJLWWFFRzZnLzk0ZDVTaHhMNnIwcUtrK3c9PQ==
  type: tx
- attributes:
  - index: true
    key: YWN0aW9u
    value: L2Nvc21vcy5nb3YudjEuTXNnU3VibWl0UHJvcG9zYWw=
  type: message
- attributes:
  - index: true
    key: cHJvcG9zYWxfaWQ=
    value: MQ==
  - index: true
    key: cHJvcG9zYWxfbWVzc2FnZXM=
    value: null
  type: submit_proposal
- attributes:
  - index: true
    key: c3BlbmRlcg==
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1o
  - index: true
    key: YW1vdW50
    value: MTBzdGFrZQ==
  type: coin_spent
- attributes:
  - index: true
    key: cmVjZWl2ZXI=
    value: Y29zbW9zMTBkMDd5MjY1Z21tdXZ0NHowdzlhdzg4MGpuc3I3MDBqNnpuOWtu
  - index: true
    key: YW1vdW50
    value: MTBzdGFrZQ==
  type: coin_received
- attributes:
  - index: true
    key: cmVjaXBpZW50
    value: Y29zbW9zMTBkMDd5MjY1Z21tdXZ0NHowdzlhdzg4MGpuc3I3MDBqNnpuOWtu
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1o
  - index: true
    key: YW1vdW50
    value: MTBzdGFrZQ==
  type: transfer
- attributes:
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1o
  type: message
- attributes:
  - index: true
    key: YW1vdW50
    value: MTBzdGFrZQ==
  - index: true
    key: cHJvcG9zYWxfaWQ=
    value: MQ==
  type: proposal_deposit
- attributes:
  - index: true
    key: bW9kdWxl
    value: Z292ZXJuYW5jZQ==
  - index: true
    key: c2VuZGVy
    value: Y29zbW9zMTY3cGZkZWM0dm5oMjg4andwY2RsaGw4a3h0NGw5enA5enNsdW1o
  type: message
gas_used: "97526"
gas_wanted: "200000"
height: "199"
info: ""
logs:
- events:
  - attributes:
    - key: receiver
      value: cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn
    - key: amount
      value: 10stake
    type: coin_received
  - attributes:
    - key: spender
      value: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    - key: amount
      value: 10stake
    type: coin_spent
  - attributes:
    - key: action
      value: /cosmos.gov.v1.MsgSubmitProposal
    - key: sender
      value: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    - key: module
      value: governance
    - key: sender
      value: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    type: message
  - attributes:
    - key: amount
      value: 10stake
    - key: proposal_id
      value: "1"
    type: proposal_deposit
  - attributes:
    - key: proposal_id
      value: "1"
    - key: proposal_messages
      value: ""
    type: submit_proposal
  - attributes:
    - key: recipient
      value: cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn
    - key: sender
      value: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    - key: amount
      value: 10stake
    type: transfer
  log: ""
  msg_index: 0
raw_log: '[{"msg_index":0,"events":[{"type":"coin_received","attributes":[{"key":"receiver","value":"cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn"},{"key":"amount","value":"10stake"}]},{"type":"coin_spent","attributes":[{"key":"spender","value":"cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh"},{"key":"amount","value":"10stake"}]},{"type":"message","attributes":[{"key":"action","value":"/cosmos.gov.v1.MsgSubmitProposal"},{"key":"sender","value":"cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh"},{"key":"module","value":"governance"},{"key":"sender","value":"cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh"}]},{"type":"proposal_deposit","attributes":[{"key":"amount","value":"10stake"},{"key":"proposal_id","value":"1"}]},{"type":"submit_proposal","attributes":[{"key":"proposal_id","value":"1"},{"key":"proposal_messages"}]},{"type":"transfer","attributes":[{"key":"recipient","value":"cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn"},{"key":"sender","value":"cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh"},{"key":"amount","value":"10stake"}]}]}]'
timestamp: "2023-07-03T09:28:43Z"
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
        key: AuWglCsCu3SP4jM7lRyqjf/1ZtaE9bcyUI48pP4dgqwE
      sequence: "1"
    tip: null
  body:
    extension_options: []
    memo: ""
    messages:
    - '@type': /cosmos.gov.v1.MsgSubmitProposal
      initial_deposit:
      - amount: "10"
        denom: stake
      messages: []
      metadata: ipfs://QmX9uKdmz2YdRw3yNtU2WPf1d2ErPYz7Qegq2ggoo3wj89
      proposer: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    non_critical_extension_options: []
    timeout_height: "0"
  signatures:
  - GlFruVKfKuPM87wOC6ae5k4idqdrdRcaiWhOzcXX8yEv8r6+8AvXn50K3/6kjbKYaEG6g/94d5ShxL6r0qKk+w==
txhash: A97AAC7744E43383E8FC5F27DBFD112DCB2079EC31490B91DCE9A19FF948F183
```

## 6.查看提案以及开始

```shell
./simd query gov proposal 1

deposit_end_time: "2023-07-05T09:28:43.875892Z"
final_tally_result:
  abstain_count: "0"
  no_count: "0"
  no_with_veto_count: "0"
  yes_count: "0"
id: "1"
messages: []
metadata: ipfs://QmX9uKdmz2YdRw3yNtU2WPf1d2ErPYz7Qegq2ggoo3wj89
status: PROPOSAL_STATUS_DEPOSIT_PERIOD
submit_time: "2023-07-03T09:28:43.875892Z"
total_deposit:
- amount: "10"
  denom: stake
voting_end_time: null
voting_start_time: null
```

查看最低deposit是多少？

```shell
./simd query gov params --output json | jq .deposit_params.min_deposit
[
  {
    "denom": "stake",
    "amount": "10000000"
  }
]
```

还需要9999990stake

```shell
./simd tx gov deposit 1 9999990stake --from bob --keyring-backend test
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
  - '@type': /cosmos.gov.v1.MsgDeposit
    amount:
    - amount: "9999990"
      denom: stake
    depositor: cosmos1ca6v3kgacl3f2w888j2248ajag9myk9c77cy58
    proposal_id: "1"
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
txhash: A86AFDE082184B133948B7AC3E220D9C12E2EA83D53722CEF2EA582FECB9BA35
```

自动到提案投票期(需要注意的是，开头设定的voting_period设定限制180s)

```shell
./simd query gov proposal 1 --output json | jq .status
"PROPOSAL_STATUS_VOTING_PERIOD"
```

## 7.投票

```shell
./simd tx gov vote 1 yes --from alice --keyring-backend test
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
  - '@type': /cosmos.gov.v1.MsgVote
    metadata: ""
    option: VOTE_OPTION_YES
    proposal_id: "1"
    voter: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
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
txhash: FC2CF98DF99D494F7FBE2BC7B89C27A8B171AFE959CEADDE0660364651C60868
```

```shell
./simd tx gov vote 1 no --from bob --keyring-backend test
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
  - '@type': /cosmos.gov.v1.MsgVote
    metadata: ""
    option: VOTE_OPTION_NO
    proposal_id: "1"
    voter: cosmos1ca6v3kgacl3f2w888j2248ajag9myk9c77cy58
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
txhash: F08E920F932643BB02F398A7E8C97B617E7F0FA2292765BF8E66FE2F504958A2
```

```shell
// 等待投票期后(3分钟)，可以看到提案已经通过。
./simd query gov proposal 1 --output json | jq .status
"PROPOSAL_STATUS_PASSED"
```

```shell
./simd query staking delegations $ALICE
delegation_responses:
- balance:
    amount: "1000000"
    denom: stake
  delegation:
    delegator_address: cosmos167pfdec4vnh288jwpcdlhl8kxt4l9zp9zslumh
    shares: "1000000.000000000000000000"
    validator_address: cosmosvaloper167pfdec4vnh288jwpcdlhl8kxt4l9zp98ytfhy
pagination:
  next_key: null
  total: "0"
```

```shell
./simd query staking delegations $BOB
delegation_responses: []
pagination:
  next_key: null
  total: "0"
```

```shell
./simd query bank balances $ALICE
balances:
- amount: "4999000000"
  denom: stake
pagination:
  next_key: null
  total: "0"
```

```shell
./simd query bank balances $BOB
balances:
- amount: "5000000000"
  denom: stake
pagination:
  next_key: null
  total: "0"
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
