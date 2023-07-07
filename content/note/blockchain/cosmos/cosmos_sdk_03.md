---
author: "li_mingxie"
title: "【cosmos-sdk笔记】Genesis File"
date: 2023-05-20T23:28:49+08:00
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

基于 simapp 和 gaia 整理了`Genesis File`内容

<!--more-->  

## 1.Genesis File

genesis文件是一个JSON文件，用于定义区块链的初始状态。  
它可以被视为您的区块链的高度0。高度为1的块将引用genesis文件作为父块。  

genesis文件中定义的状态包含所有必要的信息，  
如初始令牌分配、genesis时间、默认参数等。

## 2.创建Genesis File

可以先执行 init 方法创建默认文件。

```shell
./simd init <moniker> --chain-id <chain-id>

// 例子
./simd init test --chain-id demo
```

默认路径在 ~/.simapp/config/genesis.json

```
{
  "genesis_time": "2023-07-05T02:57:12.727578Z", // 区块启动的时间
  "chain_id": "demo", // 区块链唯一标识符
  "initial_height": "1", // 初始块的高度
  "consensus_params": { // 共识相关参数
    "block": {
      "max_bytes": "22020096", // 每个区块最大字节数
      "max_gas": "-1", // 每个区块gas的最大限制，一个区块中包含的交易使用的总Gas量不能超过此限制。
      "time_iota_ms": "1000"
    },
    //证据evidence模块的作用是确保网络的安全性和可信性，
    //通过收集和处理evidence来检测恶意行为和双重签名等问题。
    "evidence": {
      "max_age_num_blocks": "100000", // evidence的有效区块数，超过这个数量就会无效
      "max_age_duration": "172800000000000", // evidence的有效期间
      "max_bytes": "1048576" // evidence的最大字节数
    },
    "validator": {
      "pub_key_types": [ //验证这的pubKey类型
        "ed25519"
      ]
    },
    "version": {}
  },
  "app_hash": "",
  "app_state": {
    "auth": {
      "params": {
        "max_memo_characters": "256",
        "tx_sig_limit": "7",
        "tx_size_cost_per_byte": "10",
        "sig_verify_cost_ed25519": "590",
        "sig_verify_cost_secp256k1": "1000"
      },
      "accounts": [
        {
          "address": "cosmos1qs8tnw2t8l6amtzvdemnnsq9dzk0ag0z37gh3h",
          "coins": [
            {
              "denom": "uatom",
              "amount": "10000000"
            }
          ],
          "sequence_number": "0",     // 每次事务被包括在块中时，它都会递增，并用于防止重放攻击。初始值为0。
          "account_number": "0",      // 帐户的唯一标识符。账户首次交易的块中生成。
          "original_vesting": [
            {
              "denom": "uatom",
              "amount": "26306000000"
            }
          ],
          "delegated_free": null,     // 授予后可以转移的委托代币数量。一般是 null。
          "delegated_vesting": null,  // 委托代币金额。一般是 null。
          "start_time": "0",          // 开始的区块，一般是 0
          "end_time": "0"             // 结束的区块，一般是 0
        }
      ]
    },
    "authz": {
      "authorization": []
    },
    "bank": {
      "params": {
        "send_enabled": [],
        "default_send_enabled": true // enabled transfers
      },
      "balances": [],
      "supply": [],
      "denom_metadata": []
    },
    "capability": {
      "index": "1",
      "owners": []
    },
    "crisis": {
      "constant_fee": {
        "denom": "stake",
        "amount": "1000"
      }
    },
    "distribution": {
      "params": {
        "community_tax": "0.020000000000000000",          // 社区税收, 手续费和区块奖励的税收百分比，其中一部分进入社区资金池。
        "base_proposer_reward": "0.010000000000000000",   // 区块提议者奖励基准%
        "bonus_proposer_reward": "0.040000000000000000",  // 区块提议者奖励额外奖励
        "withdraw_addr_enabled": true   // 是否启用提款地址: 设置为 true，委托人可以设置不同的地址来提取他们的奖励。
      },
      "fee_pool": {
        "community_pool": []                  // 社区资金池
      },
      "delegator_withdraw_infos": [],         // 委托人提款信息列表
      "previous_proposer": "",                // 上一个区块的提议者
      "outstanding_rewards": [],              // 未提取的奖励
      "validator_accumulated_commissions": [],// 验证者累积佣金
      "validator_historical_rewards": [],     // 验证者历史奖励
      "validator_current_rewards": [],        // 验证者当前奖励
      "delegator_starting_infos": [],         // 委托人初始信息
      "validator_slash_events": []            // 验证者处罚事件
    },
    "evidence": {
      "evidence": []
    },
    "feegrant": {
      "allowances": []
    },
    "genutil": {
      "gen_txs": []
    },
    "gov": {
      "starting_proposal_id": "1",// 起始提案ID
      "deposits": [],             // 存款列表:每个提案ID的存款列表
      "votes": [],                // 投票列表:每个提案ID的投票列表
      "proposals": [],            // 提案列表:每个提案ID的提案列表
      "deposit_params": {
        "min_deposit": [          // 最少质押金
          {
            "denom": "stake",
            "amount": "10000000"
          }
        ],
        "max_deposit_period": "172800s"  // 最大质押期限
      },
      "voting_params": {
        "voting_period": "172800s"       // 投票期限
      },
      "tally_params": {
        "quorum": "0.334000000000000000",     // 法定人数:需要对提案投票的绑定代币的最低百分比
        "threshold": "0.500000000000000000",  // 通过门槛:需要投票赞成的最低百分比
        "veto_threshold": "0.334000000000000000" // 否决门槛:结果有效的最大反对门槛
      }
    },
    "group": {
      "group_seq": "0",         // 起始 groupId
      "groups": [],
      "group_members": [],
      "group_policy_seq": "0",  // 起始 group_policy_id
      "group_policies": [],
      "proposal_seq": "0",      // 起始 proposal_id
      "proposals": [],          
      "votes": []
    },
    "mint": {
      "minter": {
        "inflation": "0.130000000000000000",          // 通胀率%, 每周复利计算
        "annual_provisions": "0.000000000000000000"   // 年度供应量，是根据通胀率和绑定代币总供应计算出来
      },
      "params": {
        "mint_denom": "stake",                        // 代币名称
        "inflation_rate_change": "0.130000000000000000", //  是通胀率每年允许的最大变化量。
        "inflation_max": "0.200000000000000000",      // 通胀率允许达到的最高值
        "inflation_min": "0.070000000000000000",      // 通胀率允许达到的最低值
        "goal_bonded": "0.670000000000000000",        // 目标绑定的总供应百分比
        "blocks_per_year": "6311520"                  // 每年预计区块数量
      }
    },
    "nft": {
      "classes": [],
      "entries": []
    },
    "params": null,
    "slashing": {
      "params": {
        "signed_blocks_window": "100",                        // 签名块窗口
        "min_signed_per_window": "0.500000000000000000",      // 窗口内最低签名比例
        "downtime_jail_duration": "600s",                     // 停机监禁期限
        "slash_fraction_double_sign": "0.050000000000000000", // 双签切割比例
        "slash_fraction_downtime": "0.010000000000000000"     // 停机切割比例
      },
      "signing_infos": [],    // 签名信息
      "missed_blocks": []     // 漏块信息
    },
    "staking": {
      "pool": {
        "not_bonded_tokens": "10000000",  // 初始未绑定的代币的数量，
        "bonded_tokens": "0"              // 初始已绑定的代币数量
      },
      "params": {
        "unbonding_time": "1814400s",     // 代币解绑过程时间
        "max_validators": 100,            // 定义网络中最多活跃的验证者数量
        "max_entries": 7,                 // 这一期间解绑和转委托的最大次数限制
        "historical_entries": 10000,      
        "bond_denom": "stake",            // 货币符号
        "min_commission_rate": "0.000000000000000000"
      },
      "last_total_power": "0",            // 上一个总投票权重，除非Genesis是通过先前状态生成的。一般设为0
      "last_validator_powers": [],        // 上一个每个验证者的权重或投票权重，除非Genesis是通过先前状态生成的。一般设为null
      "validators": [],                   // 上一个已知验证者列表
      "delegations": [],                  // 上一个已知委托列表
      "unbonding_delegations": [],        // 上一个已知的解绑委托列表
      "redelegations": [],                // 上一个已知的转委托列表
      "exported": false                   // Genesis文件是否使用先前导出生成
    },
    "upgrade": {},
    "vesting": {}
  }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
