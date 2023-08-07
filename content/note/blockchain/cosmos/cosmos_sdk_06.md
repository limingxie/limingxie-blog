---
author: "li_mingxie"
title: "【cosmos-sdk笔记】x-group module"
date: 2023-07-05T23:28:49+08:00
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

链中启用组模块，用户可以创建组提交组提案。  
也可以将其视为增强型多重签名或 DAO。  

<!--more-->  

## 1.组模块的几个概念

### 组管理员(Group Admin)

创建群组的帐号就是组管理员。组管理员可以添加、删除或更改组成员账户。  
不是组的成员也可以做组的管理员。

### 组策略(Group policy)

组策略是连接 组Group 和 决策DecisionPolicy。  
为了对此帐户执行操作，提案必须得到大多数小组成员的批准或按照决策政策中的定义进行批准。为避免疑义，请注意一个组可以有多个组策略。

### 决策(Decision policy)

定义小组成员如何对提案进行投票以及如何计算投票结果的政策。决策策略与组策略相关联。这意味着一个组可以针对其每个不同的组策略有不同的决策策略。

### 提议(Proposal)

组提议的工作方式与治理提案GovernanceProposal相同。  
小组成员可以向小组发送提案以及投票，包括赞成、反对、否决或弃权。

## 2.配置启动链

simd 版本是 v0.46.6

```shell
./simd config chain-id demo
./simd config keyring-backend test

// 添加用户
./simd keys add alice
./simd keys add bob
./simd keys add carol
./simd keys add dave
./simd keys add emma
./simd keys add payee

// 为了方便写到变量
export ALICE=$(./simd keys show alice --address)
export BOB=$(./simd keys show bob --address)

// 初始化链
./simd init test --chain-id demo
./simd add-genesis-account alice 5000000000stake --keyring-backend test
./simd add-genesis-account bob 5000000000stake --keyring-backend test
./simd gentx alice 1000000stake --chain-id demo
./simd collect-gentxs

// 启动
./simd start
```

## 3.组织和成员管理

### 启动 IPFS

group_metadata.json

```json
{
  "name": "Football Association",
  "description": "Best football association",
  "group_website_url": "https://www.footbal.club",
  "group_forum_url": ""
}
```

```shell
// ipfs 添加 metadata
ipfs add group_metadata.json
ipfs daemon
```

### 创建组织成员

创建members.json文件

```json
{
    "members": [
        {
            "address": "cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0",
            "weight": "1",
            "metadata": "president"
        },
        {
            "address": "cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea",
            "weight": "1",
            "metadata": "treasurer"
        },
        {
            "address": "cosmos1lt803etucw5sj4hgwwv2mv8snqj435md56m645",
            "weight": "1",
            "metadata": "treasurer"
        }
    ]
}
```

```shell
./simd tx group create-group $ALICE "ipfs://QmP2fMonzQRRuqJDw6NjTk5dME9VamR5CwAVEca9kHPgRE" members.json
```

执行结果  

```shell
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
  - '@type': /cosmos.group.v1.MsgCreateGroup
    admin: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    members:
    - address: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
      metadata: president
      weight: "1"
    - address: cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
      metadata: treasurer
      weight: "1"
    - address: cosmos1lt803etucw5sj4hgwwv2mv8snqj435md56m645
      metadata: treasurer
      weight: "1"
    metadata: ipfs://QmP2fMonzQRRuqJDw6NjTk5dME9VamR5CwAVEca9kHPgRE
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
txhash: A178228E3A779BBCD57BF570E3CE00E4956A1DC5EB4E46465C3AF551C3397BFA
```

查看组信息

```shell
./simd query tx A178228E3A779BBCD57BF570E3CE00E4956A1DC5EB4E46465C3AF551C3397BFA --output json | jq ".events" | jq '.[] | select(.type == "cosmos.group.v1.EventCreateGroup") | .attributes'

[
  {
    "key": "Z3JvdXBfaWQ=",
    "value": "IjEi",
    "index": true
  }
]

// GROUP_ID存到变量
export GROUP_ID=$(./simd query tx A178228E3A779BBCD57BF570E3CE00E4956A1DC5EB4E46465C3AF551C3397BFA --output json | jq '.events' | jq -r '.[] | select(.type == "cosmos.group.v1.EventCreateGroup") | .attributes[0].value' | base64 --decode | jq -r '.')

// 查看ALICE 的组信息
./simd query group groups-by-admin $ALICE

// 执行结果
groups:
- admin: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
  created_at: "2023-07-03T02:23:14.550862Z"
  id: "1"
  metadata: ipfs://QmP2fMonzQRRuqJDw6NjTk5dME9VamR5CwAVEca9kHPgRE
  total_weight: "3"
  version: "1"
pagination:
  next_key: null
  total: "1"

// 用 GROUP_ID 查看组信息
./simd query group group-members $GROUP_ID
// 执行结果
members:
- group_id: "1"
  member:
    added_at: "2023-07-03T02:23:14.550862Z"
    address: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    metadata: president
    weight: "1"
- group_id: "1"
  member:
    added_at: "2023-07-03T02:23:14.550862Z"
    address: cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
    metadata: treasurer
    weight: "1"
- group_id: "1"
  member:
    added_at: "2023-07-03T02:23:14.550862Z"
    address: cosmos1lt803etucw5sj4hgwwv2mv8snqj435md56m645
    metadata: treasurer
    weight: "1"
pagination:
  next_key: null
  total: "3"
```

### 管理 group 成员

members_update.json  

```json
{
    "members": [
        {
            "address": "cosmos1lt803etucw5sj4hgwwv2mv8snqj435md56m645", // $carol
            "weight": "0" // this deletes carol
            // The metadata does not need to be mentioned
        },
        {
            "address": "cosmos1d5pca98k23x6wv56n0w3ks3g0x3vw6xf3zvlcv", //daveaddr
            "weight": "1",
            "metadata": "treasurer"
        },
        {
            "address": "cosmos1yu5fukcjp0rz4vk47xwx9upynrfycwtvft7q80", //emmaaddr
            "weight": "1",
            "metadata": "player"
        }
    ]
}
```

```shell
// 更新组成员
./simd tx group update-group-members $ALICE $GROUP_ID members_update.json

// 执行结果
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
  - '@type': /cosmos.group.v1.MsgUpdateGroupMembers
    admin: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    group_id: "1"
    member_updates:
    - address: cosmos1lt803etucw5sj4hgwwv2mv8snqj435md56m645
      metadata: ""
      weight: "0"
    - address: cosmos1d5pca98k23x6wv56n0w3ks3g0x3vw6xf3zvlcv
      metadata: treasurer
      weight: "1"
    - address: cosmos1yu5fukcjp0rz4vk47xwx9upynrfycwtvft7q80
      metadata: player
      weight: "1"
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
txhash: E74CDC0FD3395E942E8E692DF162C727E4CC5DD47A4F64D9B25DA8573E168ABB

// 查看更新结果
./simd query group group-members $GROUP_ID

// 执行结果
members:
- group_id: "1"
  member:
    added_at: "2023-07-03T02:27:36.485818Z"
    address: cosmos1yu5fukcjp0rz4vk47xwx9upynrfycwtvft7q80
    metadata: player
    weight: "1"
- group_id: "1"
  member:
    added_at: "2023-07-03T02:27:36.485818Z"
    address: cosmos1d5pca98k23x6wv56n0w3ks3g0x3vw6xf3zvlcv
    metadata: treasurer
    weight: "1"
- group_id: "1"
  member:
    added_at: "2023-07-03T02:23:14.550862Z"
    address: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    metadata: president
    weight: "1"
- group_id: "1"
  member:
    added_at: "2023-07-03T02:23:14.550862Z"
    address: cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
    metadata: treasurer
    weight: "1"
pagination:
  next_key: null
  total: "4"
```

## 4.创建group policy

创建组策略内容 json  
policy.json  

* 提案的投票时间最长为 10 分钟。
* 一项提案只需一票即可通过。

```json
{
    "@type": "/cosmos.group.v1.ThresholdDecisionPolicy",
    "threshold": "1",
    "windows": {
        "voting_period": "10m",
        "min_execution_period": "0s"
    }
}
```

```shell
// 创建组策略
./simd tx group create-group-policy $ALICE $GROUP_ID "{\"name\":\"quick turnaround\",\"description\":\"\"}" policy.json

// 执行结果
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
  - '@type': /cosmos.group.v1.MsgCreateGroupPolicy
    admin: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    decision_policy:
      '@type': /cosmos.group.v1.ThresholdDecisionPolicy
      threshold: "1"
      windows:
        min_execution_period: 0s
        voting_period: 600s
    group_id: "1"
    metadata: '{"name":"quick turnaround","description":""}'
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
txhash: 886652122BC69747F8130FEE50C90D93C277F7645120F19993E216A78F2C8CCE

// 组策略ID 存到变量
export GROUP_POLICY_ADDRESS=$(./simd query tx 886652122BC69747F8130FEE50C90D93C277F7645120F19993E216A78F2C8CCE --output json | jq '.events' | jq -r '.[] | select(.type == "cosmos.group.v1.EventCreateGroupPolicy") | .attributes[0].value' | base64 --decode | jq -r '.')

// 通过 GROUP_ID 查看策略
./simd query group group-policies-by-group $GROUP_ID
// 执行结果
group_policies:
- address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
  admin: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
  created_at: "2023-07-03T02:29:57.599509Z"
  decision_policy:
    '@type': /cosmos.group.v1.ThresholdDecisionPolicy
    threshold: "1"
    windows:
      min_execution_period: 0s
      voting_period: 600s
  group_id: "1"
  metadata: '{"name":"quick turnaround","description":""}'
  version: "1"
pagination:
  next_key: null
  total: "1"

// 查看策略地址
./simd query group group-policies-by-group $GROUP_ID --output json | jq -r '.group_policies[0].address'

// 执行结果
cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd

// 给策略发币，要不提案结束后，没有钱给 payee
./simd tx bank send $ALICE $GROUP_POLICY_ADDRESS  5000stake
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
  - '@type': /cosmos.bank.v1beta1.MsgSend
    amount:
    - amount: "5000"
      denom: stake
    from_address: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
    to_address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
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
txhash: 5C579B918D17FCC51952103BD830F1C921808F1A47B91C0F28C0D43516C3F4AC
```

## 5.创建proposal

### 新建提案 IPFS

proposal_metadata.json

```json
{
  "title": "Pay the utilities bill",
  "authors": "Bob Smith",
  "summary": "Pay the energy bill of the association",
  "details": "",
  "proposal_forum_url": "https://football.club/proposal/nov-utility-bills",
  "vote_option_context": "Yes means pay the energy bill. No means to not pay the energy bill and have no more warm water.",
}
```

```shell
ipfs add proposal_metadata.json
added QmTF5BshCyRqnvfGtvYU3gYjfcXaMjLxnNFxdENmTaw6nX proposal_metadata.json
```

### 创建提案信息

proposal.json

```json
{
    "group_policy_address": "cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd" , // the $GROUP_POLICY_ADDRESS
    // array of proto-JSON-encoded sdk.Msgs
    "messages": [
        {
            "@type": "/cosmos.bank.v1beta1.MsgSend",
            "from_address": "cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd", // the $GROUP_POLICY_ADDRESS
            "to_address": "cosmos1nyvdmlssz6e9sc7ddp37lvxjgqmrm8cgngg204", // payee
            "amount": [
                {
                    "denom": "stake",
                    "amount": "100"
                }
            ]
        }
    ],
    "metadata": "ipfs://QmTF5BshCyRqnvfGtvYU3gYjfcXaMjLxnNFxdENmTaw6nX",
    "proposers": [ "cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea" ] // $BOB
}
```

```shell
./simd tx group submit-proposal proposal.json --from bob

// 执行结果
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
  - '@type': /cosmos.group.v1.MsgSubmitProposal
    exec: EXEC_UNSPECIFIED
    group_policy_address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
    messages:
    - '@type': /cosmos.bank.v1beta1.MsgSend
      amount:
      - amount: "100"
        denom: stake
      from_address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
      to_address: cosmos1nyvdmlssz6e9sc7ddp37lvxjgqmrm8cgngg204
    metadata: ipfs://QmTF5BshCyRqnvfGtvYU3gYjfcXaMjLxnNFxdENmTaw6nX
    proposers:
    - cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
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
txhash: 3C402AA94BE6DBB386D75150B101622991859B33B6CCB8AE1F910DA0D70BF1A9

// 提案 ID 存到变量
export PROPOSAL_ID=$(./simd query tx 3C402AA94BE6DBB386D75150B101622991859B33B6CCB8AE1F910DA0D70BF1A9 --output json | jq '.events' | jq -r '.[] | select(.type == "cosmos.group.v1.EventSubmitProposal") | .attributes[0].value' | base64 --decode | jq -r '.')

// 查看策略
./simd query group proposals-by-group-policy $GROUP_POLICY_ADDRESS --output json | jq '.proposals[0]'

//执行结果
{
  "id": "1",
  "group_policy_address": "cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd",
  "metadata": "ipfs://QmTF5BshCyRqnvfGtvYU3gYjfcXaMjLxnNFxdENmTaw6nX",
  "proposers": [
    "cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea"
  ],
  "submit_time": "2023-07-03T02:34:59.883957Z",
  "group_version": "2",
  "group_policy_version": "1",
  "status": "PROPOSAL_STATUS_SUBMITTED",
  "final_tally_result": {
    "yes_count": "0",
    "abstain_count": "0",
    "no_count": "0",
    "no_with_veto_count": "0"
  },
  "voting_period_end": "2023-07-03T02:44:59.883957Z",
  "executor_result": "PROPOSAL_EXECUTOR_RESULT_NOT_RUN",
  "messages": [
    {
      "@type": "/cosmos.bank.v1beta1.MsgSend",
      "from_address": "cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd",
      "to_address": "cosmos1nyvdmlssz6e9sc7ddp37lvxjgqmrm8cgngg204",
      "amount": [
        {
          "denom": "stake",
          "amount": "100"
        }
      ]
    }
  ]
}
```

```shell
// 查看提案信息
./simd query group proposal $PROPOSAL_ID --output json | jq '.proposal.final_tally_result'

// 执行结果
{
  "yes_count": "0",
  "abstain_count": "0",
  "no_count": "0",
  "no_with_veto_count": "0"
}
```

### 提案过程

```shell
// 查看策略状态
./simd query group proposals-by-group-policy $GROUP_POLICY_ADDRESS --output json | jq -r '.proposals[0].status'
// PROPOSAL_STATUS_SUBMITTED

// alice 同意
./simd tx group vote $PROPOSAL_ID $ALICE VOTE_OPTION_YES "agree"

// 执行结果
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
  - '@type': /cosmos.group.v1.MsgVote
    exec: EXEC_UNSPECIFIED
    metadata: agree
    option: VOTE_OPTION_YES
    proposal_id: "1"
    voter: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
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
txhash: D76DD40FDE665F72565FF3C328C238B14DE8676919A7AB857606817043742F45

./simd tx group vote $PROPOSAL_ID $BOB VOTE_OPTION_YES "aye"
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
  - '@type': /cosmos.group.v1.MsgVote
    exec: EXEC_UNSPECIFIED
    metadata: aye
    option: VOTE_OPTION_YES
    proposal_id: "1"
    voter: cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
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
txhash: 45DA109D39742DDB17D867ACCCA011AF4542BAC022421F0F18DB142B55208466


// 查看提案信息
./simd query group tally-result $PROPOSAL_ID
tally:
  abstain_count: "0"
  no_count: "0"
  no_with_veto_count: "0"
  yes_count: "1"

// 查看提案详细
./simd query group proposal $PROPOSAL_ID
proposal:
  executor_result: PROPOSAL_EXECUTOR_RESULT_NOT_RUN
  final_tally_result:
    abstain_count: "0"
    no_count: "0"
    no_with_veto_count: "0"
    yes_count: "1"
  group_policy_address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
  group_policy_version: "1"
  group_version: "2"
  id: "1"
  messages:
  - '@type': /cosmos.bank.v1beta1.MsgSend
    amount:
    - amount: "100"
      denom: stake
    from_address: cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd
    to_address: cosmos1nyvdmlssz6e9sc7ddp37lvxjgqmrm8cgngg204
  metadata: ipfs://QmTF5BshCyRqnvfGtvYU3gYjfcXaMjLxnNFxdENmTaw6nX
  proposers:
  - cosmos1nvsswjsfwcyplqy73zz485cps54ar7r2we9uea
  status: PROPOSAL_STATUS_ACCEPTED
  submit_time: "2023-07-03T02:34:59.883957Z"
  voting_period_end: "2023-07-03T02:44:59.883957Z"
```

### 立即执行提案结束

```shell
./simd tx group exec $PROPOSAL_ID --from alice
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
  - '@type': /cosmos.group.v1.MsgExec
    executor: cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
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
txhash: A088774E6C590BA008618AE3AE77F70CFACA05664B5080BCB7EC6FC05B653AE6
```

```shell
//查看提案结果会报错，是因为它已被完全删除。
./simd query group proposal $PROPOSAL_ID
Error: rpc error: code = Unknown desc = load proposal: not found: unknown request
```

### 查看收款人有没有收到款

```shell
./simd query bank balances cosmos1nyvdmlssz6e9sc7ddp37lvxjgqmrm8cgngg204
balances:
- amount: "100"
  denom: stake
pagination:
  next_key: null
  total: "0"

```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
