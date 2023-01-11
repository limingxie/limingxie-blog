---
author: "li_mingxie"
title: "【区块链笔记】ETH_dapp(01)"
date: 2922-12-05T23:28:49+08:00
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


这篇文章简单的整理了`以太坊web3监听事件`。  <!--more-->  

## 1.监听事件

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.WebsocketProvider('ws://127.0.0.1:8546'));

var _from = '0x64b113923012af9af62a76584b65a12b90f817c3';
var _to = '0x0d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f';

var abi = [{ "inputs": [], "stateMutability": "nonpayable", "type": "constructor" }, { "anonymous": false, "inputs": [{ "indexed": false, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "address", "name": "to", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "Sent", "type": "event" }, { "stateMutability": "payable", "type": "fallback" }, { "inputs": [{ "internalType": "address", "name": "add", "type": "address" }], "name": "getMinterBalance", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "mint", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "minter", "outputs": [{ "internalType": "address", "name": "", "type": "address" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "sendCoin", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "testaa", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }];
var contractAddress = '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9';
var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());

contractInstance.events.Sent({
    from: _from,
    to: _to,
    amount: 1234
}, function (error, event) {
    if (error)
        console.log('error:', error);
    else
        console.log('event:', event);
})
    .on("connected", function (subscriptionId) {
        console.log('subscriptionId:', subscriptionId);
    })
    .on('data', function (event) {
        console.log('data:', event); // 与上述可选的回调结果相同
    })
    .on('changed', function (event) {
        // 从本地数据库中删除事件
        console.log('changed:', event);
    })
    .on('error', function (error, receipt) { // 如果交易被网络拒绝并带有交易收据，第二个参数将是交易收据。
        if (error)
            console.log('error"', error);
        else
            console.log('error:', receipt);
    });
```

## 2.调用时间，看看监听结果

```js
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var _from = '0x64b113923012af9af62a76584b65a12b90f817c3';
var _to = '0x0d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f';
var amount_mint = 666666;
var amount_send = 111111;

var abi = [{ "inputs": [], "stateMutability": "nonpayable", "type": "constructor" }, { "anonymous": false, "inputs": [{ "indexed": false, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "address", "name": "to", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "Sent", "type": "event" }, { "stateMutability": "payable", "type": "fallback" }, { "inputs": [{ "internalType": "address", "name": "add", "type": "address" }], "name": "getMinterBalance", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "mint", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "minter", "outputs": [{ "internalType": "address", "name": "", "type": "address" }], "stateMutability": "view", "type": "function" }, { "inputs": [{ "internalType": "address", "name": "receiver", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" }], "name": "sendCoin", "outputs": [], "stateMutability": "payable", "type": "function" }, { "inputs": [], "name": "testaa", "outputs": [{ "internalType": "uint256", "name": "", "type": "uint256" }], "stateMutability": "view", "type": "function" }];
var contractAddress = '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9';

var contractInstance = new web3.eth.Contract(abi, contractAddress.toLowerCase());

//直接在web3里解锁账户
web3.eth.personal.unlockAccount(_from, '1234', (err, res) => {
    if (err) {
        console.log("error11: ", err);
    }
    else {
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
});
```

## 3.执行结果

```bash
Result11:  3444441
Result22:  777777
Result33:  0x8ba1afae4c32e6c502a3e6e59e17f27a56356970e90ca4b1b08449607fa3ae9b
Result44:  0xe710317d011844cd043ac5f9c13527f98919585fd8a1409737ddd40cea00aafd
```

监听事件的日志

```bash
$ node test05.js
subscriptionId: 0xd7b0aa63db86efa155048f89715f7d90
data: {
  address: '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9',
  blockNumber: 608,
  transactionHash: '0xe710317d011844cd043ac5f9c13527f98919585fd8a1409737ddd40cea00aafd',
  transactionIndex: 1,
  blockHash: '0x43a44c1509c563f78f990a663a5941e82acd5b8d3d632073bc8af1f7811ce9af',
  logIndex: 0,
  removed: false,
  id: 'log_6a30a883',
  returnValues: Result {
    '0': '0x64B113923012Af9AF62a76584b65A12b90f817c3',
    '1': '0x0D2c1eE7f8c2a254F18514f48e0d75d3D625Cb9f',
    '2': '111111',
    from: '0x64B113923012Af9AF62a76584b65A12b90f817c3',
    to: '0x0D2c1eE7f8c2a254F18514f48e0d75d3D625Cb9f',
    amount: '111111'
  },
  event: 'Sent',
  signature: '0x3990db2d31862302a685e8086b5755072a6e2b5b780af1ee81ece35ee3cd3345',
  raw: {
    data: '0x00000000000000000000000064b113923012af9af62a76584b65a12b90f817c30000000000000000000000000d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f000000000000000000000000000000000000000000000000000000000001b207',
    topics: [
      '0x3990db2d31862302a685e8086b5755072a6e2b5b780af1ee81ece35ee3cd3345'
    ]
  }
}
event: {
  address: '0xB955Fe4E0616762D773E8D403DBCBD8C87A994c9',
  blockNumber: 608,
  transactionHash: '0xe710317d011844cd043ac5f9c13527f98919585fd8a1409737ddd40cea00aafd',
  transactionIndex: 1,
  blockHash: '0x43a44c1509c563f78f990a663a5941e82acd5b8d3d632073bc8af1f7811ce9af',
  logIndex: 0,
  removed: false,
  id: 'log_6a30a883',
  returnValues: Result {
    '0': '0x64B113923012Af9AF62a76584b65A12b90f817c3',
    '1': '0x0D2c1eE7f8c2a254F18514f48e0d75d3D625Cb9f',
    '2': '111111',
    from: '0x64B113923012Af9AF62a76584b65A12b90f817c3',
    to: '0x0D2c1eE7f8c2a254F18514f48e0d75d3D625Cb9f',
    amount: '111111'
  },
  event: 'Sent',
  signature: '0x3990db2d31862302a685e8086b5755072a6e2b5b780af1ee81ece35ee3cd3345',
  raw: {
    data: '0x00000000000000000000000064b113923012af9af62a76584b65a12b90f817c30000000000000000000000000d2c1ee7f8c2a254f18514f48e0d75d3d625cb9f000000000000000000000000000000000000000000000000000000000001b207',
    topics: [
      '0x3990db2d31862302a685e8086b5755072a6e2b5b780af1ee81ece35ee3cd3345'
    ]
  }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
