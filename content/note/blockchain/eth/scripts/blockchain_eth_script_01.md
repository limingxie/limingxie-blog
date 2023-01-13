---
author: "li_mingxie"
title: "【区块链笔记】ETH_script_合约编译脚本(01)"
date: 2022-12-07T23:28:49+08:00
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

这篇文章主要是`以太坊script_合约编译脚本`。  <!--more-->  

创建目录

```bash
mkdir compiled
mkdir contracts
mkdir scripts
mkdir test
```

## 1.脚本功能

保存合约编译后的内容。

1. 清楚之前编译的内容
2. 读取合约内容
3. 编译合约内容
4. 有错误存储错误日志
5. 无错误存储编译后的内容

## 2.实例代码

```js
const fs = require('fs-extra');
const path = require('path');
const solc = require('solc');
const contractPath = path.resolve(__dirname, '../contracts', 'Car.sol');

// clean 
const compiledDir = path.resolve(__dirname, '../compiled');
fs.removeSync(compiledDir);
fs.ensureDirSync(compiledDir);

const contractSource = fs.readFileSync(contractPath, { encoding: 'utf8' });

const input = {
    language: 'Solidity',
    sources: {
        'Car': {
            content: contractSource,
        },
    },
    settings: {
        outputSelection: {
            '*': {
                '*': ['*'],
            },
        },
    },
};

const compileSolCode = JSON.parse(solc.compile(JSON.stringify(input)));

// check errors
if (Array.isArray(result.errors) && result.errors.length) {
    throw new Error(result.errors[0]);
}

Object.keys(compileSolCode.contracts).forEach(name => {
    const contractName = name.replace(/^:/, '');
    const filePath = path.resolve(__dirname, '../compiled', `${contractName}.json`);
    fs.outputJsonSync(filePath, compileSolCode.contracts[name]);
    console.log(`save compiled contract ${contractName} to ${filePath}`);
})
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
