---
author: "li_mingxie"
title: "【区块链笔记】ETH_dapp_前端界面以及js功能(02)"
date: 2022-12-06T23:28:49+08:00
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

这篇文章主要是`以太坊dapp_前端界面以及js功能`。  <!--more-->  

## 1.执行投票以及查询票数的方法

```js
// 执行投票以及查询方法

const fs = require('fs');
const Web3 = require('web3');
const web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
const sleep = (delay) => new Promise((resolve) => setTimeout(resolve, delay));

var _from = "0x09C092F708F0Ad7a3BfcCCBEC30B8B57DBd4aF71";
var abi = JSON.parse(fs.readFileSync('./contract/Voting_sol_Voting.abi', { encoding: 'utf8' }));

var contractAddress = '0x6fdf2f1915247c87945cf8e0c2b2fa93899e13aa';

var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());

    //投票后等0.1秒再查询投票数
async function voteAndSearch(){
    contractInstance.methods.voteForCandidate('0x123').send({ from: _from }, (err, res) => {
        if (err)
            console.log("error11: ", err);
        else
            console.log("Result11: ", res);
    });
    
    await sleep(100);
    
    contractInstance.methods.totalVotesFor('0x123').call({ from: _from }, (err, res) => {
        if (err)
            console.log("error22: ", err);
        else
            console.log("Result22: ", res);
    });
}

voteAndSearch();
```

```bash
$ node test08.js
Result11:  0x21720a1b03e1d7d4a8a8cb0eff7975369c2d55600e859efaff599e13a98eeb1b
Result22:  1
```

## 2.创建一个简单的html界面

#### index.html

```html
<!DOCTYPE html>
<html>

<head>
    <title>Voting DApp</title>
    <link href='https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css' rel='stylesheet' type='text/css'>
</head>
<script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"> </script>
<script src="https://code.jquery.com/jquery-3.1.1.slim.min.js"> </script>
<script src="./index.js"></script>
<body class="container">
    <h1>A Simple Voting Application</h1>
    <div class="table-responsive">
        <table class="table table-bordered">
            <thead>
                <tr>
                    <th>Candidate</th>
                    <th>Votes</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>0x123</td>
                    <td id="candidate-1"></td>
                </tr>
                <tr>
                    <td>0x234</td>
                    <td id="candidate-2"></td>
                </tr>
                <tr>
                    <td>0x345</td>
                    <td id="candidate-3"></td>
                </tr>
                <tr>
                    <td>0x456</td>
                    <td id="candidate-4"></td>
                </tr>
            </tbody>
        </table>
    </div>
    <input type="text" id="candidate" />
    <a href="#" onclick="voteForCandidate()" class="btn btn-primary">Vote</a>
</body>
</html>
```

#### index.js

```js
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
var abi = JSON.parse('[{ "inputs": [{ "internalType": "bytes32[]", "name": "candidateNames", "type": "bytes32[]" }], "stateMutability": "nonpayable", "type": "constructor" }, { "inputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "name": "candidateList", "outputs": [{ "internalType": "bytes32", "name": "", "type": "bytes32" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "bytes32", "name": "candidate", "type": "bytes32" }], "name": "totalVotesFor", "outputs": [{ "internalType": "uint8", "name": "", "type": "uint8" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "bytes32", "name": "candidate", "type": "bytes32" }], "name": "validCandidate", "outputs": [{ "internalType": "bool", "name": "", "type": "bool" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "bytes32", "name": "candidate", "type": "bytes32" }], "name": "voteForCandidate", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [{ "internalType": "bytes32", "name": "", "type": "bytes32" }], "name": "votesReceived", "outputs": [{ "internalType": "uint8", "name": "", "type": "uint8" }], "stateMutability": "view", "type": "function" }]');
var contractAddress = '0xca87b92a8bb9a5f70daa979e6f0c6fdc9e9e0256';
var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());
var _from = '0x09C092F708F0Ad7a3BfcCCBEC30B8B57DBd4aF71'

var candidates = {
    "0x123": "candidate-1",
    "0x234": "candidate-2",
    "0x345": "candidate-3",
    "0x456": "candidate-4"
};

function voteForCandidate() {
    let candidateName = $("#candidate").val();
    try {
        contractInstance.methods.voteForCandidate(candidateName).send({ from: _from }, (err, res) => {
            if (err) {
                console.log('err:', err);
            } else {
                let id = candidates[candidateName];
                contractInstance.methods.totalVotesFor(candidateName).call({ from: _from }, (err, res) => {
                    if (err)
                        console.log("error: ", err);
                    else
                        $("#" + id).html(res);
                });
            }
        })
    } catch (err) {
        console.log('err:', err)
    }
}
$(document).ready(function () {
    var candidateList = Object.keys(candidates);
    for (var i = 0; i < candidateList.length; i++) {
        let name = candidateList[i];
        contractInstance.methods.totalVotesFor(name).call({ from: _from }, (err, res) => {
            if (err)
                console.log("error: ", err);
            else
                $("#" + candidates[name]).html(res);
        });
    }
});
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
