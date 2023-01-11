---
author: "li_mingxie"
title: "【区块链笔记】ETH_web3简介(01)"
date: 2022-11-30T23:28:49+08:00
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


这篇文章简单的整理了`以太坊web3简介`相关的内容。  <!--more-->  

## 1.web3.js

* Web3 JavaScript app API
* web3.js 是一个JavaScript API库。  
    要使DApp在以太坊上运行，我们可以使用web3.js库提供的web3对象
* web3.js通过RPC调用与本地节点通信，它可以用于任何暴露了RPC层的以太坊节点
* web3包含eth对象 -web3.eth(专门与以太坊区块链交互)和shh对象 -web3.shh(用于与 Whisper 交互）

## 2.web3模块加载

* 首先需要将web3模块安装在项目中: npm install web3@0.20.1
* 然后创建一个web3实例，设置一个“provider”
* 为了保证我们的MetaMask设置好的provider不被覆盖掉，在引入web3之前
我们一般要做当前环境检查(以v0.20.1为例):

```sol
if (typeof web3 !== 'undefined') {
    web3 = new Web3(web3.currentProvider); 
} else {
    web3 = new Web3(new Web3.providers .HttpProvider("http://localhost:8545"));
}
```

## 3.异步回调(callback)

* web3js API 设计的最初目的，主要是为了和本地RPC节点共同使用，  
    所以默认情况下发送的是同步HTTP请求
* 如果要发送异步请求，可以在函数的最后一个参数位置上，传入一个回调函数。  
    回调函数是可选(optioanl)的
* 我们一般采用的回调风格是所谓的“错误优先”，  
    例如:

```sol
web3.eth.getBlock(48, function(error, result){
    if(!error)
        console.log(JSON.stringify(result));
    else
        console.error(error);
});
```

## 4.回调 Promise 事件(v1.0.0)

* 为了帮助web3集成到不同标准的所有类型项目中，1.0.0版本提供了多种方式来处理异步函数。  
    大多数的web3对象允许将一个回调函数作为最后一个函数参数传入，  
    同时会返回一个promise 用于链式函数调用。
* 以太坊作为一个区块链系统，一次请求具有不同的结束阶段。  
    为了满足这样的要求，1.0.0 版本将这类函数调用的返回值包成一个“承诺事件”(promiEvent)，  
    这是一个 promise 和 EventEmitter 的结合体。
* PromiEvent的用法就像promise一样，另外还加入了.on，.once 和.off方法  

```sol
web3.eth.sendTransaction({from: '0x123...', data: '0x432...'}) 
.once('transactionHash', function(hash){ ... })
.once('receipt', function(receipt){ ... })
.on('confirmation', function(confNumber, receipt){ ... })
.on('error', function(error){ ... })
.then(function(receipt){ // will be fired once the receipt is mined });
```

## 5.应用二进制接口(ABI)

* web3.js 通过以太坊智能合约的json 接口(Application Binary Interface，ABI)创建一个JavaScript对象，用来在js代码中描述
* 函数(functions)
    type:函数类型，默认“function”，也可能是“constructor”
    constant, payable, stateMutability:函数的状态可变性
    inputs, outputs: 函数输入、输出参数描述列表
* 事件(events)
    type:类型，总是“event”
    inputs:输入对象列表，包括 name、type、indexed

## 6.批处理请求(batch requests)

* 批处理请求允许我们将请求排序，然后一起处理它们。
* 注意:批量请求不会更快。实际上，在某些情况下，一次性地发出许多请求会更快，因为请求是异步处理的。
* 批处理请求主要用于确保请求的顺序，并串行处理。

```sol
var batch = web3.createBatch(); 
batch.add(web3.eth.getBalance.request('0x0000000000000000000000000000000000000000', 'latest', callback)); 
batch.add(web3.eth.contract(abi).at(address).balance.request(address, callback2)); 
batch.execute();
```

## 7.大数处理(big numbers)

* JavaScript中默认的数字精度较小，所以web3.js会自动添加一个依赖库BigNumber，专门用于大数处理
* 对于数值，我们应该习惯把它转换成BigNumber对象来处理

```sol
var balance = new BigNumber('131242344353464564564574574567456'); 
// or var balance = web3.eth.getBalance(someAddress); 
balance.plus(21).toString(10); 
//"131242344353464564564574574567477"
```

* BigNumber.toString(10)对小数只保留20位浮点精度。  
    所以推荐的做法是，我们内部总是用wei来表示余额(大整数)，  
    只有在需要显示给用户看的时候才转换为ether或其它单位

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
