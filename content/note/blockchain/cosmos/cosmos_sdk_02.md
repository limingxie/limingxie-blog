---
author: "li_mingxie"
title: "【cosmos-sdk笔记】simd 常用的命令整理"
date: 2023-05-19T23:28:49+08:00
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

整理了，simd链 启用后的简单的操作命令。

<!--more-->  

## key

```shell
// 添加
./simd keys add alice
// 查看
./simd keys list
./simd keys show alice 
./simd keys show alice --address
```

## group

```
./simd query group
./simd tx group
```

## bank

```shell
//simd query bank balances [address]
./simd query bank balances cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0
//simd tx bank send [from_key_or_address] [to_address] [amount]
./simd tx bank send cosmos1nxqdcm8tjrnf3c4x4082kjdhmmeafvrhkdk5z0 cosmos1afk9zr2hn2jsac63h4hm60vl9z3e5u69gndzf7c99cqge3vzwjzsfwkgpd  5000stake
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
