---
author: "li_mingxie"
title: "【区块链笔记】ETH_web3.eth相关API(03)"
date: 2022-12-02T23:28:49+08:00
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


这篇文章简单的整理了`以太坊web3.eth相关API`相关的内容。  <!--more-->  

参考地址：<https://web3js.readthedocs.io/en/v1.8.1/>

## 1.账户相关

#### coinbase 查询

* 同步:web3.eth.coinbase
* 异步:web3.eth.getCoinbase( (err, res)=>console.log(res) )
* v1.0.0:web3.eth.getCoinbase().then(console.log)

#### 账户查询

* 同步:web3.eth.accounts
* 异步:web3.eth.getAccounts( (err, res)=>console.log(res) )
* v1.0.0:web3.eth.getAccounts().then(console.log)

## 3.区块相关

#### 区块高度查询

* 同步:web3.eth. blockNumber
* 异步:web3.eth.getBlockNumber( callback )

#### gasPrice 查询

* 同步:web3.eth.gasPrice
* 异步:web3.eth.getGasPrice( callback )

#### 区块查询

* 同步:web3.eth.getBlockNumber(hashStringOrBlockNumber[ ,returnTransactionObjects] )
* 异步:web3.eth.getBlockNumber( hashStringOrBlockNumber, callback )

#### 块中交易数量查询

* 同步: web3.eth.getBlockTransactionCount( hashStringOrBlockNumber )
* 异步: web3.eth.getBlockTransactionCount( hashStringOrBlockNumber, callback )

## 4.交易相关

#### 余额查询

* 同步:web3.eth.getBalance(addressHexString [, defaultBlock])
* 异步:web3.eth.getBalance(addressHexString [, defaultBlock][, callback])

#### 交易查询

* 同步:web3.eth.getTransaction(transactionHash)
* 异步:web3.eth.getTransaction(transactionHash [, callback])

## 5.交易执行相关

* 交易收据查询(已进块)
* 同步:web3.eth.getTransactionReceipt(hashString)
* 异步:web3.eth.getTransactionReceipt(hashString [, callback])
* 估计 gas 消耗量
* 同步:web3.eth.estimateGas(callObject)
* 异步:web3.eth.estimateGas(callObject [, callback])

## 6.发送交易

* web3.eth.sendTransaction(transactionObject [, callback])
* 交易对象:
    from:发送地址
    to:接收地址，如果是创建合约交易，可不填
    value:交易金额，以wei为单位，可选
    gas:交易消耗gas上限，可选
    gasPrice:交易gas单价，可选
    data:交易携带的字串数据，可选
    nonce:整数 nonce 值，可选

## 7.消息调用

* web3.eth.call(callObject[,defaultBlock][,callback])
* 参数:
    调用对象:与交易对象相同，只是from也是可选的
    默认区块:默认“latest”，可以传入指定的区块高度
    回调函数，如果没有则为同步调用

```sol
var result = web3.eth.call({ to: "0xc4abd0339eb8d57087278718986382264244252f",data:"0xc6888fa10000000000000000000000000000000000000000000000000000000000000003" });
console.log(result);
```

## 8. 日志过滤(事件监听)

* web3.eth.filter( filterOptions [ , callback ] )
    // filterString 可以是 'latest' or 'pending'
    var filter = web3.eth.filter(filterString);
    // 或者可以填入一个日志过滤 options
    var filter = web3.eth.filter(options);
    // 监听日志变化
    filter.watch(function(error, result){ if (!error) console.log(result); });
    // 还可以用传入回调函数的方法，立刻开始监听日志
    web3.eth.filter(options, function(error, result){
    if (!error) console.log(result);
    });

## 9.合约相关 —— 创建合约

* web3.eth.contract
    var MyContract = web3.eth.contract(abiArray);
    // 通过地址初始化合约实例
    var contractInstance = MyContract.at(address);
    // 或者部署一个新合约
    var contractInstance = MyContract.new([constructorParam1][, constructorParam2], {data: '0x12345...', from: myAccount, gas: 1000000});

## 10.调用合约函数

* 可以通过已创建的合约实例，直接调用合约函数
    // 直接调用，自动按函数类型决定用 sendTransaction 还是 call
    myContractInstance.myMethod(param1 [, param2, ...] [, transactionObject] [, defaultBlock] [, callback]);
    // 显式以消息调用形式 call 该函数
    myContractInstance.myMethod.call(param1 [, param2, ...] [,transactionObject] [, defaultBlock] [, callback]);
    // 显式以发送交易形式调用该函数
    myContractInstance.myMethod.sendTransaction(param1 [, param2, ...] [, transactionObject] [, callback]);

## 11.监听合约事件

* 合约的 event 类似于 filter，可以设置过滤选项来监听
    var event = myContractInstance.MyEvent({valueA: 23}[, additionalFilterObject])
    // 监听事件
    event.watch(function(error, result){ if (!error) console.log(result); });
    //还可以用传入回调函数的方法，立刻开始监听事件
    var event = myContractInstance.MyEvent([{valueA: 23}][, additionalFilterObject] , function(error, result){ if (!error) console.log(result);} );

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
