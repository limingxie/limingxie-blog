---
author: "li_mingxie"
title: "【区块链笔记】ETH_script_合约部署脚本(04)"
date: 2022-12-08T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "web3",
]
---

这篇文章主要是`以太坊script_合约部署脚本`。  <!--more-->  

## 1.部署前需要做的准备

1. 确定与以太坊节点的通信连接  
    因为开发的时候都是用本地连接做测试，真正部署的时候需要确认与以太坊节点的通信连接
2. 部署的账户余额需要大于0  
    当然而容易忽略饿事情

## 2.部署实例

```js
const Web3 = require('web3');
const web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
const path = require('path');

// 1. 拿到 abi 和 bytecode
const filePath = path.resolve(__dirname, '../compiled/Car.json');
const compileSolCode = require(filePath);

var abi = compileSolCode.Car.abi;
var bytecode = compileSolCode.Car.evm.bytecode.object.toString();

(async () => {
    // 2. 获取钱包里面的账户
    let accounts = await web3.eth.getAccounts();
    // 3. 创建合约实例并且部署
    let result = await new web3.eth.Contract(abi)
        .deploy({ data: bytecode, arguments: ["BMW", 1000000] })
        .send({ from: accounts[0], gas: 5000000 })
    console.log("合约部署成功:", result);
    console.log("合约地址:", result.options.address);
})();
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
