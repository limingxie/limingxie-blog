---
author: "li_mingxie"
title: "【cosmos-sdk-笔记】cosmovisor 简单实现(实践)"
date: 2023-08-08T23:28:49+08:00
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

这一篇简单的测试一下 cosmoviser 的自动更新功能。
<!--more-->  

## 1.创建项目

首先引用cosmos-sdk简单的写了tvote模块。  

```bash
git clone https://github.com/taoyuans/mingxie.git

cd mingxie
git checkout main_v1

go build -ldflags="-X github.com/cosmos/cosmos-sdk/version.Version=v0.1.0"  -o ./build/mingxied ./cmd/mingxied/main.go
cd build
```

## 2.初始化链

```bash
rm -rf ~/.mingxie
./mingxied config chain-id test
./mingxied config keyring-backend test
./mingxied config broadcast-mode sync
./mingxied init test --chain-id test --overwrite
```

把genesis.json的 voting_period 改为 20s.

```bash
cat <<< $(jq '.app_state.gov.voting_params.voting_period = "20s"' $HOME/.mingxie/config/genesis.json) > $HOME/.mingxie/config/genesis.json
```

新建一个validator账户以及创建创始tx

```bash
./mingxied keys add validator
./mingxied add-genesis-account validator 1000000000stake --keyring-backend test
./mingxied gentx validator 1000000stake --chain-id test
./mingxied collect-gentxs
```

## 3.初始化 cosmoviser

```bash
export DAEMON_NAME=mingxied
export DAEMON_HOME=$HOME/.mingxie

export DAEMON_RESTART_AFTER_UPGRADE=true
```

```bash
mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
cp ./mingxied $DAEMON_HOME/cosmovisor/genesis/bin
```

启动

```bash
cosmovisor run start
```

## 4.更新版本

先查看目前的版本(version：v0.1.0)

```bash
curl --location 'http://127.0.0.1:26657/abci_info'

{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "response": {
      "data": "mingxie",
      "version": "v0.1.0",
      "last_block_height": "2",
      "last_block_app_hash": "8EMqgVjd3eb74xenDL+s9aByATSr8MytEi2yC699CZ8="
    }
  }
}
```

重新编译应用(v0.1.0 => v0.1.1)

```bash
git checkout main_v1_1

go build -ldflags="-X github.com/cosmos/cosmos-sdk/version.Version=v0.1.1"  -o ./build/mingxied ./cmd/mingxied/main.go
cd build
```

把编译好的新应用拷贝到 upgrade 文件夹下  

```bash
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v0.1.1/bin
cp ./mingxied $DAEMON_HOME/cosmovisor/upgrades/v0.1.1/bin
```

查看 gov 模块的 module account

```bash
./mingxied query auth module-account gov
account:
  '@type': /cosmos.auth.v1beta1.ModuleAccount
  base_account:
    account_number: "5"
    address: cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn
    pub_key: null
    sequence: "0"
  name: gov
  permissions:
  - burner
```

创建提案 json

```bash
vi proposal.json
```

```javascript
{
 "messages": [
  {
   "@type": "/cosmos.upgrade.v1beta1.MsgSoftwareUpgrade",
   "authority": "cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn", // 注意这里是 module account
   "plan": {
    "name": "v0.1.1", // 这个名字和 /cosmovisor/upgrades/v0.1.1 这个文件明需要一致
    "time": "0001-01-01T00:00:00Z",
    "height": "50",
    "info": "aa",
    "upgraded_client_state": null
   }
  }
 ],
 "metadata": "ipfs://QmPngptoFqAJVApdgvpW2pQXYZj7RscbHnoc5SWQ9em4JR",
 "deposit": "10stake"
}
```

申请提案

```bash
./mingxied tx gov submit-proposal proposal.json --from validator --keyring-backend test
```

```bash
./mingxied tx gov deposit 1 10000000stake --from validator --yes
./mingxied tx gov vote 1 yes --from validator --yes
```

查看提案状态是否通过

```bash
./mingxied query gov proposal 1

deposit_end_time: "2023-08-11T12:40:25.748192Z"
final_tally_result:
  abstain_count: "0"
  no_count: "0"
  no_with_veto_count: "0"
  yes_count: "1000000"
id: "1"
messages:
- '@type': /cosmos.upgrade.v1beta1.MsgSoftwareUpgrade
  authority: cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn
  plan:
    height: "50"
    info: aa
    name: v0.1.1
    time: "0001-01-01T00:00:00Z"
    upgraded_client_state: null
metadata: ipfs://QmPngptoFqAJVApdgvpW2pQXYZj7RscbHnoc5SWQ9em4JR
status: PROPOSAL_STATUS_PASSED
submit_time: "2023-08-09T12:40:25.748192Z"
total_deposit:
- amount: "10000010"
  denom: stake
voting_end_time: "2023-08-09T12:41:00.841716Z"
voting_start_time: "2023-08-09T12:40:40.841716Z"
```

看看执行的日志

```bash
11:23AM INF Timed out dur=4967.315 height=50 module=consensus round=0 step=1
  ... ...
11:23AM ERR UPGRADE "v0.1.1" NEEDED at height: 50:
11:23AM ERR CONSENSUS FAILURE!!! err="UPGRADE \"v0.1.1\" NEEDED at height: 50: 
  ... ...
11:23AM INF service stop impl={"Logger":{}} module=consensus msg={} wal=/Users/limingxie/.mingxie/data/cs.wal/wal
11:23AM INF service stop impl={"Dir":"/Users/limingxie/.mingxie/data/cs.wal","Head":{"ID":"OO0H4Abo1eSV:/Users/limingxie/.mingxie/data/cs.wal/wal","Path":"/Users/limingxie/.mingxie/data/cs.wal/wal"},"ID":"group:OO0H4Abo1eSV:/Users/limingxie/.mingxie/data/cs.wal/wal","Logger":{}} module=consensus msg={} wal=/Users/limingxie/.mingxie/data/cs.wal/wal
11:23AM INF daemon shutting down in an attempt to restart module=cosmovisor
11:23AM INF starting to take backup of data directory backup start time=2023-08-11T11:23:10+08:00 module=cosmovisor
11:23AM INF backup completed backup completion time=2023-08-11T11:23:10+08:00 backup saved at=/Users/limingxie/.mingxie/data-backup-2023-8-11 module=cosmovisor time taken to complete backup=8.23779
11:23AM INF pre-upgrade command does not exist. continuing the upgrade. module=cosmovisor
11:23AM INF upgrade detected, relaunching app=mingxied module=cosmovisor
11:23AM INF running app args=["start"] module=cosmovisor path=/Users/limingxie/.mingxie/cosmovisor/upgrades/v0.1.1/bin/mingxied
```

看看版本更新的对不对？(version：v0.1.1)

```bash
curl --location 'http://127.0.0.1:26657/abci_info'

{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "response": {
      "data": "mingxie",
      "version": "v0.1.1",
      "last_block_height": "53",
      "last_block_app_hash": "HzXrbNSZssi22xXjM+ZCHIBf5JBemmYPs/Np3KHmhcQ="
    }
  }
}
```

下一篇简单的整理如何修改代码。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
