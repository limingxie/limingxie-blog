---
author: "li_mingxie"
title: "【区块链笔记】ETH_dapp_开发环境准备(01)"
date: 2022-12-05T23:28:49+08:00
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

这篇文章主要是`以太坊dapp_开发环境做准备`。  <!--more-->  

## 1.创建目录以及安装依赖包

```bash
mkdir test_voting_dapp
cd test_voting_dapp

npm init
npm install ganache-cli --save-dev
npm install web3-cli
npm install solc
```

ganache是比geth更方便的构建开发环境。  
他会默认给你创建10个账号和私钥供开发使用。  

```bash
$ node_modules/.bin/ganache-cli
Ganache CLI v6.12.2 (ganache-core: 2.13.2)
```

## 2.创建Solidity合约

```sol
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.17;

contract Voting {
    //候选人的票数
    mapping(bytes32 => uint8) public votesReceived;
    //候选人列表
    bytes32[] public candidateList;

    //初始化候选人
    constructor(bytes32[] memory candidateNames) {
        candidateList = candidateNames;
    }

    //查看投票数
    function totalVotesFor(bytes32 candidate) public view returns (uint8) {
        require(validCandidate(candidate));

        return votesReceived[candidate];
    }

    //给候选人投票
    function voteForCandidate(bytes32 candidate) public {
        require(validCandidate(candidate));
        votesReceived[candidate] += 1;
    }

    //验证是否在候选人内
    function validCandidate(bytes32 candidate) public view returns (bool) {
        for (uint256 i = 0; i < candidateList.length; i++) {
            if (candidateList[i] == candidate) {
                return true;
            }
        }
        return false;
    }
}
```

## 3.部署合约

```js
const fs = require('fs');
const solc = require('solc');
const Web3 = require('web3');
const web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var _from = "0x09C092F708F0Ad7a3BfcCCBEC30B8B57DBd4aF71";
var source = fs.readFileSync('./contract/Voting.sol', { encoding: 'utf8' });

const input = {
    language: 'Solidity',
    sources: {
        'Voting': {
            content: source,
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
var compileSolCode = JSON.parse(solc.compile(JSON.stringify(input)));
var abi = compileSolCode.contracts.Voting.Voting.abi;
var bytecode = compileSolCode.contracts.Voting.Voting.evm.bytecode.object.toString();

var deployObj = { from: _from, data: bytecode, gas: 900000 };
var VotingContract = new web3.eth.Contract(abi);


VotingContract.deploy(
    {
        data: bytecode,
        arguments: [
            ['0x123','0x234','0x345','0x456']
        ]
    }).send(deployObj, console.log);
```

```bash
$ node test07.js
null 0xce12c6ed020b4ec5559d87378ed60a2f2597198edf559d75aa41e3feebcf72a7
```

执行ganache的进程日志

```bash
Transaction: 0xce12c6ed020b4ec5559d87378ed60a2f2597198edf559d75aa41e3feebcf72a7
Contract created: 0xca87b92a8bb9a5f70daa979e6f0c6fdc9e9e0256
Gas usage: 445821
Block Number: 15
Block Time: Wed Jan 11 2023 17:33:01 GMT+0800 (China Standard Time)
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
