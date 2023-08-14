---
author: "li_mingxie"
title: "【cosmos-sdk-笔记】cosmovisor 简单实现(代码:基础)"
date: 2023-08-10T23:28:49+08:00
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

这一篇整理使用cosmovisor更新时如何修改代码。
<!--more-->  

## 1.下载代码

我使用cosmos-sdk简单的写了tvote模块。  

```bash
git clone https://github.com/taoyuans/mingxie.git

cd mingxie
git checkout main_v1_1
```

这里有 2 个分支:  
`main_v1` 是升级前的代码(v0.1.0)  
`main_v1_1` 是升级后的代码(v0.1.1)  

## 2.项目结构

```
app
| ... ...
|___app.go  
|___upgrades // => 创建更新目录，后续的更新代码卸载这里
| |___v01   // => 版本号，因为是 v0.1 系列版本，所以取名为 01 这个可以随意
| | |___upgrades.go // => 此版本更新逻辑
| | |___constants.go // => 一些使用的常量
|___upgrades.go // => 整体更新逻辑
|... ...
```

## 3.app/upgrade/v01/constants.go

这里的 UpgradeName 会在同一个目录的upgrades.go使用。
需要注意的是这里的 UpgradeName 和 我们申请 upgrade proposal的 name 要一致。  
简单说 proposal.json 里的 name 字段和一致。

```go
const (
 UpgradeName        = "v0.1.1"
)
```

## 4.app/upgrade/v01/upgrades.go

这里创建 CreateUpgradeHandler  
需要注意的是返回是 mm.RunMigrations(ctx, configurator, vm)  

```go
package v01

import (
 sdk "github.com/cosmos/cosmos-sdk/types"
 "github.com/cosmos/cosmos-sdk/types/module"
 upgradetypes "github.com/cosmos/cosmos-sdk/x/upgrade/types"
)

func CreateUpgradeHandler(mm *module.Manager, configurator module.Configurator) upgradetypes.UpgradeHandler {
 return func(ctx sdk.Context, plan upgradetypes.Plan, vm module.VersionMap) (module.VersionMap, error) {
  //
  return mm.RunMigrations(ctx, configurator, vm)
 }
}
```

## 5.app/upgrades.go

这里的setupUpgradeHandlers是在 app.go 里设置

```go
package app

import (
 "fmt"
 v01 "mingxie/app/upgrades/v01"

 tvotemodulekeeper "mingxie/x/tvote/keeper"

 storetypes "github.com/cosmos/cosmos-sdk/store/types"
 "github.com/cosmos/cosmos-sdk/types/module"
 upgradetypes "github.com/cosmos/cosmos-sdk/x/upgrade/types"
)

func (app *App) setupUpgradeHandlers(configurator module.Configurator, keeper tvotemodulekeeper.Keeper) {
 app.UpgradeKeeper.SetUpgradeHandler(v01.UpgradeName, v01.CreateUpgradeHandler(app.mm, configurator))

//  upgradeInfo, err := app.UpgradeKeeper.ReadUpgradeInfoFromDisk()
//  if err != nil {
//   panic(fmt.Errorf("failed to read upgrade info from disk: %w", err))
//  }

//  if app.UpgradeKeeper.IsSkipHeight(upgradeInfo.Height) {
//   return
//  }

//  var storeUpgrades *storetypes.StoreUpgrades

//  switch upgradeInfo.Name {
//  case v01.UpgradeName:
//   fmt.Println("更新 v0.1.1")
//  }

//  if storeUpgrades != nil {
//   app.SetStoreLoader(upgradetypes.UpgradeStoreLoader(upgradeInfo.Height, storeUpgrades))
//  }
}
```

## 6.app/app.go

在 app.LoadLatestVersion() 方法执行之前  
设置app.setupUpgradeHandlers(app.configurator, app.TvoteKeeper)

```go
 // setupUpgradeHandlers should be called before `LoadLatestVersion()`
 // because StoreLoad is sealed after that
 app.setupUpgradeHandlers(app.configurator, app.TvoteKeeper)

 if loadLatest {
  if err := app.LoadLatestVersion(); err != nil {
   tmos.Exit(err.Error())
  }
 }
```

## 7.运行

以上的步骤是最简单的修改，之后根据前一篇文章。  
使用 cosmovisor 运行就可以了。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
