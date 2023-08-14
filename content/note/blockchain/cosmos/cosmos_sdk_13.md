---
author: "li_mingxie"
title: "【cosmos-sdk-笔记】cosmovisor 简单实现(代码:修改存储结构)"
date: 2023-08-13T23:28:49+08:00
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
git checkout main_v1_2
```

这里有 2 个分支:  
`main_v1` 是升级前的代码(v0.1.0)  
`main_v1_1` 是升级后的代码(v0.1.1)  
`main_v1_2` 是升级后的代码添加逻辑(v0.1.1)  

## 2.项目结构

```
app
| ... ...
|___app.go  
|___upgrades // => 创建更新目录，后续的更新代码卸载这里
| |___v011   // => 版本号，因为是 v0.1.1 系列版本，所以取名为 011 这个可以随意
|   |___upgrades.go // => 此版本更新逻辑
|   |___constants.go // => 一些使用的常量
|___upgrades.go // => 整体更新逻辑
|... ...

x/tvote
| ... ...
|___keeper 
| |___ ... ...
| |___migrations.go   
|___migrations 
|   |___v2
|     |___migrations.go
|... ...
```

## 3.x/tvote/migrations/v2/migrations.go

这里的MigrateStore方法要处理具体的逻辑。  
我这里简单的添加了一个字段，计算出来后存储了。  

```go
package v011 // migrations.go
import (
 "fmt"
 "mingxie/x/tvote/types"

 "github.com/cosmos/cosmos-sdk/codec"
 storetypes "github.com/cosmos/cosmos-sdk/store/types"
 sdk "github.com/cosmos/cosmos-sdk/types"
 paramtypes "github.com/cosmos/cosmos-sdk/x/params/types"
)

func UpdateParams(ctx sdk.Context, paramStore *paramtypes.Subspace) error {

 // paramStore.Set(ctx, []byte(types.VoterKey), "foobar")

 return nil
}
func MigrateStore(ctx sdk.Context, storeKey storetypes.StoreKey, cdc codec.BinaryCodec) error {
 store := ctx.KVStore(storeKey)

 return migrateValuesWithPrefix(store, cdc)
}
func migrateValuesWithPrefix(store sdk.KVStore, cdc codec.BinaryCodec) error {
 oldStoreIter := store.Iterator(nil, nil)

 for ; oldStoreIter.Valid(); oldStoreIter.Next() {
  oldKey := oldStoreIter.Key()
  oldVal := store.Get(oldKey)

  newKey, newVal := migrateValue(cdc, oldKey, oldVal)
  store.Set(newKey, newVal)
  // store.Delete(oldKey) // Delete old key, value
 }

 return nil
}
func migrateValue(cdc codec.BinaryCodec, oldKey []byte, oldVal []byte) (newKey []byte, newVal []byte) {
 var newVoter types.Voter
 err := cdc.Unmarshal(oldVal, &newVoter)
 if err != nil {
  fmt.Println(err)
 }

 newVoter.Level = uint32(newVoter.Age - newVoter.Vid)

 newKey = oldKey
 newVal = cdc.MustMarshal(&newVoter)
 return
}

```

## 4.tvote/x/keeper/migrations.go

这里主要是控制调用哪个 migration

```go
package keeper // migrations.go
import (
 v2 "mingxie/x/tvote/migrations/v2"

 sdk "github.com/cosmos/cosmos-sdk/types"
)

type Migrator struct {
 keeper Keeper
}

func NewMigrator(keeper Keeper) Migrator {
 return Migrator{keeper: keeper}
}
func (m Migrator) Migrate1to2(ctx sdk.Context) error {
 if err := v2.UpdateParams(ctx, &m.keeper.paramstore); err != nil {
  return err
 }

 return v2.MigrateStore(ctx, m.keeper.storeKey, m.keeper.cdc)
}

```

## 5.tvote/x/module.go

最后在 module.go 里注册migration 方法。

```go
// RegisterServices registers a gRPC query service to respond to the module-specific gRPC queries
func (am AppModule) RegisterServices(cfg module.Configurator) {
 migrator := keeper.NewMigrator(am.keeper)
 if err := cfg.RegisterMigration(types.ModuleName, 1, migrator.Migrate1to2); err != nil {
  panic(fmt.Errorf("failed to migrate %s to v011: %w", types.ModuleName, err))
 }

 types.RegisterMsgServer(cfg.MsgServer(), keeper.NewMsgServerImpl(am.keeper))
 types.RegisterQueryServer(cfg.QueryServer(), am.keeper)
}
```

## 7.运行

使用 cosmovisor 运行就可以了。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
