---
author: "li_mingxie"
title: "【cosmos-sdk-笔记】修改各模块的运行参数"
date: 2023-08-20T23:28:49+08:00
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

这一篇整理了如何修改运行中的链的配置参数。  
<!--more-->  

## 1.sdk 版本选择

我刚开始测试的时候使用的是 v0.47.3。  
但是用 ignite 测试 relayer 的跨链时有问题。  
所以特意降到 v0.46.6 写了测试项目了。  

结果因为这个版本，这次害我不浅...ㅠㅠ  
为了就这么个一个修改voting_period，折腾了2天了，哎～  

先说结论：  
v0.46.6版本的，<appd> tx gov draft-proposal 方法里没有 /cosmos.gov.v1.MsgUpdateParams 方法。  
而且网上的都是老资料，很容易误导人。  
新版本都没有 change-param 这个命令了...^^;;  

```bash
    /cosmos.feegrant.v1beta1.MsgRevokeAllowance
    /cosmos.gov.v1.MsgDeposit
    /cosmos.gov.v1.MsgExecLegacyContent
    /cosmos.gov.v1.MsgSubmitProposal
    /cosmos.gov.v1.MsgVote
    /cosmos.gov.v1.MsgVoteWeighted
    /cosmos.gov.v1beta1.MsgDeposit
    /cosmos.gov.v1beta1.MsgSubmitProposal
    /cosmos.gov.v1beta1.MsgVote
    /cosmos.gov.v1beta1.MsgVoteWeighted
↓ ▸ /cosmos.group.v1.MsgCreateGroup
```

后来我在discord里提问，有个开发给我解答说要用/cosmos.gov.v1.MsgUpdateParams  
这才我想到改用别的版本试一试。  
改到最新 v0.47.4 版本以后，就可以看到/cosmos.gov.v1.MsgUpdateParams方法了. 哎～  

## 2.配置环境

#### start.sh

```bash
#!/bin/bash
rm -rf ~/.mingxie

export DAEMON_NAME=mingxied
export DAEMON_HOME=$HOME/.mingxie
export DAEMON_RESTART_AFTER_UPGRADE=true

./mingxied config chain-id test
./mingxied config keyring-backend test
./mingxied config broadcast-mode sync
./mingxied init test --chain-id test --overwrite

cat <<< $(jq '.app_state.gov.params.voting_period = "20s"' $HOME/.mingxie/config/genesis.json) > $HOME/.mingxie/config/genesis.json
cat <<< $(jq '.app_state.gov.params.max_deposit_period = "30s"' $HOME/.mingxie/config/genesis.json) > $HOME/.mingxie/config/genesis.json

./mingxied keys add validator1 
./mingxied genesis add-genesis-account validator1 1000000000stake --keyring-backend test 
./mingxied genesis gentx validator1 1000000stake --chain-id test 
./mingxied genesis collect-gentxs


mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
cp ./mingxied $DAEMON_HOME/cosmovisor/genesis/bin

cosmovisor run start
```

## 3.修改配置参数

先查看voting_period是多少？  

```bash
./mingxied query gov param voting
voting_period: "20000000000"
```

#### proposal.json

```json
{
    "messages": [
        {
            "@type": "/cosmos.gov.v1.MsgUpdateParams",
            "authority": "cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn",
            "params": {
                "min_deposit": [
                    {
                        "denom": "stake",
                        "amount": "10000000"
                    }
                ],
                "max_deposit_period": "30s",
                "voting_period": "40s",
                "quorum": "0.334000000000000000",
                "threshold": "0.500000000000000000",
                "veto_threshold": "0.334000000000000000",
                "min_initial_deposit_ratio": "0.000000000000000000",
                "burn_vote_quorum": false,
                "burn_proposal_deposit_prevote": false,
                "burn_vote_veto": true
            }
        }
    ],
    "metadata": "ipfs://CID",
    "deposit": "3333stake",
    "title": "update voting_period 40s",
    "summary": "ok"
}
```

#### update.sh

```bash

#!/bin/bash

./simd tx gov submit-proposal proposal.json --from validator1 --keyring-backend test -y

sleep 6s

./simd tx gov deposit 1 100000000stake --from validator1 --yes

sleep 6s

./simd tx gov vote 1 yes --from validator1 --yes 

sleep 6s

./simd query gov proposal 1
```

查看结果，已改好了～

```bash
./simd query gov param voting
voting_period: "40000000000"
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
