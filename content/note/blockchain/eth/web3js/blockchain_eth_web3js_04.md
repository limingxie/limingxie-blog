---
author: "li_mingxie"
title: "【区块链笔记】ETH_web3简单实例(04)"
date: 2022-12-03T23:28:49+08:00
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


这篇文章简单的整理了`以太坊web3简单实例`。  <!--more-->  

## 1.部署一个合约

先准备一个只能合约，编译abi文件和bin文件

#### coin.sol

```sol
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

contract Coin{
    address public minter;
    uint public testaa; 
    mapping (address=>uint) balances;
    event Sent(address from, address to, uint amount);
    constructor()public{
        minter = msg.sender;
        testaa = 11;
    }
    function mint(address receiver, uint amount) public payable {
        require(msg.sender == minter);
        balances[receiver] += amount;
    }
    function sendCoin(address receiver, uint amount) public payable {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        balances[receiver] += amount;

        emit Sent(msg.sender, receiver, amount);
    }
    function getBalance(address add) public view returns(uint) {
        return balances[add];
    }

    fallback() external payable{
        testaa = 22;
    }
}
```

```bash
solcjs --abi --bin coin.sol
```

#### 部署合约

直接把abi文件和bin文件内容复制过来。  
需要注意的是bin文件是16进制的所以需要加0x前缀

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var _from = "0x64b113923012af9af62a76584b65a12b90f817c3";

var abi = [{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"address","name":"from","type":"address"},{"indexed":false,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"amount","type":"uint256"}],"name":"Sent","type":"event"},{"stateMutability":"payable","type":"fallback"},{"inputs":[{"internalType":"address","name":"add","type":"address"}],"name":"getBalance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"receiver","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"mint","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"minter","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"receiver","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"sendCoin","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"testaa","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"}];
var contractByteCode ='0x608060405234801561001057600080fd5b50336000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff160217905550600b6001819055506105dd806100686000396000f3fe60806040526004361061004e5760003560e01c8063075461721461005857806308be6fd81461008357806340c10f19146100c057806390b98a11146100dc578063c343a462146100f85761004f565b5b60166001819055005b34801561006457600080fd5b5061006d610123565b60405161007a91906103c0565b60405180910390f35b34801561008f57600080fd5b506100aa60048036038101906100a5919061040c565b610147565b6040516100b79190610452565b60405180910390f35b6100da60048036038101906100d59190610499565b610190565b005b6100f660048036038101906100f19190610499565b610242565b005b34801561010457600080fd5b5061010d610379565b60405161011a9190610452565b60405180910390f35b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1681565b6000600260008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020549050919050565b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16146101e857600080fd5b80600260008473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282546102379190610508565b925050819055505050565b80600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054101561028e57600080fd5b80600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282546102dd919061053c565b9250508190555080600260008473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282546103339190610508565b925050819055507f3990db2d31862302a685e8086b5755072a6e2b5b780af1ee81ece35ee3cd334533838360405161036d93929190610570565b60405180910390a15050565b60015481565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b60006103aa8261037f565b9050919050565b6103ba8161039f565b82525050565b60006020820190506103d560008301846103b1565b92915050565b600080fd5b6103e98161039f565b81146103f457600080fd5b50565b600081359050610406816103e0565b92915050565b600060208284031215610422576104216103db565b5b6000610430848285016103f7565b91505092915050565b6000819050919050565b61044c81610439565b82525050565b60006020820190506104676000830184610443565b92915050565b61047681610439565b811461048157600080fd5b50565b6000813590506104938161046d565b92915050565b600080604083850312156104b0576104af6103db565b5b60006104be858286016103f7565b92505060206104cf85828601610484565b9150509250929050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b600061051382610439565b915061051e83610439565b9250828201905080821115610536576105356104d9565b5b92915050565b600061054782610439565b915061055283610439565b925082820390508181111561056a576105696104d9565b5b92915050565b600060608201905061058560008301866103b1565b61059260208301856103b1565b61059f6040830184610443565b94935050505056fea26469706673582212209eb228b6778839e74484e914d168e185e743335e76ba23d096bbc0a4f44bd7f764736f6c63430008110033';
var deployObj = {from: _from, data: contractByteCode, gas: 900000};
var CoinContract = new web3.eth.Contract(abi);

CoinContract.deploy({data: contractByteCode}).send(deployObj, console.log);
```

```bash
$ node test02.js
null 0x86f27c1d25934cb4d5f78d9d41635c792b15a0ef1d07abccb0ed60054e9a07a7
```

geth日志获取合约账号

```bash
INFO [01-07|23:17:07.467] Submitted contract creation              
hash=0x86f27c1d25934cb4d5f78d9d41635c792b15a0ef1d07abccb0ed60054e9a07a7 
from=0x64B113923012Af9AF62a76584b65A12b90f817c3 
nonce=37 
contract=0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9 
value=0
```

返回的是合约地址，保留着下面用。

## 2.转账的简单实例

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var arg = process.argv.splice(2);

if (!arg || arg.length != 2) {
    console.log("Parameter error!");
    return;
}

var _from = '0x64b113923012af9af62a76584b65a12b90f817c3';
var _to = arg[0];

var _value = arg[1];

web3.eth.sendTransaction({ from: _from, to: _to, value: _value }, (err, res) => {
    if (err)
        console.log("error: ", err);
    else
        console.log("Result: ", res);
});
```

执行结果

```bash
$ node test01.js 0x0d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f 400000000000000
Result:  0xd5dbd8129fff30faec2a4d92064f019c5b1f19ec63ba23c3c09ac2eddf22be21
```

## 3.调用合约方法

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var _from = '0x64b113923012af9af62a76584b65a12b90f817c3';
var _to = '0x0d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f';
var amount_mint = 666666;
var amount_send = 111111;

var abi = [{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"address","name":"from","type":"address"},{"indexed":false,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"amount","type":"uint256"}],"name":"Sent","type":"event"},{"stateMutability":"payable","type":"fallback"},{"inputs":[{"internalType":"address","name":"add","type":"address"}],"name":"getMinterBalance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"receiver","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"mint","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"minter","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"receiver","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"sendCoin","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[],"name":"testaa","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"}];
var contractAddress = '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9';

var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());

contractInstance.methods.getMinterBalance(_from).call({ from: _from }, (err, res) => {
    if (err)
        console.log("error11: ", err);
    else
        console.log("Result11: ", res);
});

contractInstance.methods.getMinterBalance(_to).call({ from: _from }, (err, res) => {
    if (err)
        console.log("error22: ", err);
    else
        console.log("Result22: ", res);
});

contractInstance.methods.mint(_from, amount_mint).send({ from: _from }, (err, res) => {
    if (err)
        console.log("error33: ", err);
    else
        console.log("Result33: ", res);
});

contractInstance.methods.sendCoin(_to, amount_send).send({ from: _from }, (err, res) => {
    if (err)
        console.log("error44: ", err);
    else
        console.log("Result44: ", res);
});
```

执行结果(后台需不停的挖矿)

```bash
# 第一次
$ node test03.js
Result11:  0
Result22:  0
Result33:  0x61228452cdef29beef4846e5c66a12cb2a4bb8a8542457a9eaa622cff4a9ed86
Result44:  0x6e694767c094cec7d7c6bc6a1a39e8362d1cc0a5f4f73aaf80172f2f7e5f6ef2

# 第二次
$ node test03.js
Result11:  555555
Result22:  111111
Result33:  0xe710357b8565a78b6d200d0a6e19c1be2a72a972cff5a2bcf311a277a798d80f
Result44:  0xca87ebd6adb36ff07191f7793c9f3a1166e52f40ba7532d00dca657874768419

# 第三次
$ node test03.js
Result11:  1111110
Result22:  222222
Result33:  0x2e5e3e753b24026e6713beeff048ad1b316dbdf883e22f0517bff8c1f2a99cb4
Result44:  0x170d624625571b1d62d42cfb6511626b379089a896168133483d08b3fe3c25da
```

## 4.完善一下调用方法

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var arg = process.argv.splice(2);

if (!arg || arg.length != 2) {
    console.log("Parameter error!");
    return;
}

var _from = '0x64b113923012af9af62a76584b65a12b90f817c3';
var _to = arg[0];
var amount_mint = 666666;
var amount_send = arg[1];

var abi = [{ "inputs": [], "stateMutability": "nonpayable", "type": "constructor" }, { "anonymous": false, "inputs": [{ "indexed": false, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "address", "name": "to", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "Sent", "type": "event" }, { "stateMutability": "payable", "type": "fallback" }, { "inputs": [{ "internalType": "address", "name": "add", "type": "address" }], "name": "getMinterBalance", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "mint", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "minter", "outputs": [{ "internalType": "address", "name": "", "type": "address" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "sendCoin", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "testaa", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }];
var contractAddress = '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9';
var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());

//直接在web3里解锁账户
web3.eth.personal.unlockAccount(_from, '1234', (err, res) => {
    if (err)
        console.log("error11: ", err);
    else
        contractTest();
});

//为了在回调函数里调用，打包在一起
async function contractTest() {
    contractInstance.methods.getMinterBalance(_from).call({ from: _from }, (err, res) => {
        if (err)
            console.log("error11: ", err);
        else
            console.log("Result11: ", res);
    });

    contractInstance.methods.getMinterBalance(_to).call({ from: _from }, (err, res) => {
        if (err)
            console.log("error22: ", err);
        else
            console.log("Result22: ", res);
    });

    contractInstance.methods.mint(_from, amount_mint).send({ from: _from }, (err, res) => {
        if (err)
            console.log("error33: ", err);
        else
            console.log("Result33: ", res);
    });

    contractInstance.methods.sendCoin(_to, amount_send).send({ from: _from }, (err, res) => {
        if (err)
            console.log("error44: ", err);
        else
            console.log("Result44: ", res);
    });
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
