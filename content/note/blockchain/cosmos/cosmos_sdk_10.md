---
author: "li_mingxie"
title: "【cosmos-sdk-笔记】cosmovisor简介"
date: 2023-08-07T23:28:49+08:00
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

cosmos-sdk提供 upgrade 模块，可以优雅的升级新的功能
<!--more-->  

## 1.cosmovisor安装

cosmovisor 的代码虽然是和 cosmos-sdk 一起管理，但它是独立运行的应用。  

```bash
git clone git@github.com:cosmos/cosmos-sdk
cd cosmos-sdk
git checkout cosmovisor/vx.x.x
make cosmovisor
```

```
cp cosmovisor/cosmovisor $GOPATH/bin/cosmovisor

$ cosmovisor -h
Usage:
  cosmovisor [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  run         Run an APP command.
  version     Prints the version of Cosmovisor.
```

## 2.cosmovisor的参数

cosmovisor运行中的参数是通过`环境变量`传递的。  

**DAEMON_HOME**  
cosmovisor/ 目录的位置 (比如 $HOME/.gaiad, $HOME/.regend, $HOME/.simd, etc.).  

**DAEMON_NAME**  
别名 (比如 gaiad, regend, simd, etc.).  

**DAEMON_ALLOW_DOWNLOAD_BINARIES**  
(可选), 如果设置为true,会自动下载更新的二进制文件，但是为了安全最好不要用(怎么能保证下载的时候不断网或报错)。

**DAEMON_RESTART_AFTER_UPGRADE**  
(可选, default = true), 如果是true, 升级后自动重启, fasle 有可能升级后将停止运行。  

**DAEMON_RESTART_DELAY**  
(可选, default none), 升级之前的延迟等待时间 (比如 1s).

**DAEMON_POLL_INTERVAL**  
(可选, default 300 milliseconds), 升级计划文件的轮询时间 (比如 1s).  

**DAEMON_DATA_BACKUP_DIR**  
设置自定一个备份目录，不设置默认使用DAEMON_HOME.  

**UNSAFE_SKIP_BACKUP**  
(defaults to false), 设置为true，直接升级而不执行备份。  
(false，默认)在尝试升级前备份数据。建议使用UNSAFE_SKIP_BACKUP=false。

**DAEMON_PREUPGRADE_MAX_RETRIES**  
(defaults to 0). 失败重试的次数最大可以 31 次。  

## 3.DAEMON_HOME 结构

如果DAEMON_HOME的路径和 appd 路径设置成一样会这样。  
也可以设置其他路径。  

```
.mingxie
├── config
├── data
└── cosmovisor
```

```
.
├── current -> genesis or upgrades/<name>
├── genesis
│   └── bin
│       └── $DAEMON_NAME
└── upgrades
    └── <name>
        ├── bin
        │   └── $DAEMON_NAME
        └── upgrade-info.json
```

## 4.使用步骤

1. 首先配置好相对应的环境变量，因为cosmovisor的参数都是环境变量的方式传递
2. 创建<DAEMON_HOME>/cosmovisor 目录
3. 创建<DAEMON_HOME>/cosmovisor/genesis/bin 目录
4. 创建<DAEMON_HOME>/cosmovisor/upgrades/<name>/bin 目录
5. 把 appd 拷贝到对应的 current，genesis，upgrade的bin目录中(Auto-Download功能就不做整理了。官方不建议使用。)

然后开始，实际操作在下一篇文章中整理。

> cosmovisor run start

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
