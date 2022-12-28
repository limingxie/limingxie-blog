---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity简介(01)"
date: 2022-11-22T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "solidity",
]
---


这篇文章简单的整理了`以太坊solidity`相关的信息。  <!--more-->  

## 1.Solidity是什么

Solidity 是一门面向合约的、为实现智能合约而创建的高级编程语言。  
这门语言受到了C++，Python和Javascript语言的影响，设计的目的是能在以太坊虚拟(EVM)上运行。

* Solidity是静态类型语言，支持继承、库和复杂的用户定义类 型等特性。
* 内含的类型除了常见编程语言中的标准类型，还包括`address`等以太坊独有的类型，Solidity源码文件通常以 .sol 作为扩展名
* 目前尝试Solidity编程的最好的方式是使用Remix。  
    Remix是一个基于Web浏览器的IDE，它可以让你编写Solidity智能合约，然后部署并运行该智能合约。

## 2.Solidity语言特性

Solidity的语法接近于JavaScript，是一种面向对象的语言。但作为一种真正意义上运行在网络上的去中心合约，它又有很多的不同:

* 以太坊底层基于帐户，而不是UTXO，所以增加了一个特殊的`address`的数据类型用于定位用户和合约账户。
* 语言内嵌框架`支持支付`。提供了`payable`等关键字，可以在语言层面直接支持支付。
* 使用区块链进行数据存储。数据的每一个状态都可以永久存储，所以在使用时需要`确定变量使用内存`，还是区块链存储。
* 运行环境是在去中心化的网络上，所以需要强调合约或函数执行的调用的方式。
* 不同的异常机制。一旦出现异常，所有的执行都将会被回撤，这主要是为了保证合约执行的原子性，以避免中间状态出现的数据不一致。

## 3.Solidity源码和智能合约

Solidity源代码要成为可以运行在以太坊上的智能合约需要经历如下的步骤:

1. 用 Solidity编写的智能合约源代码需要先使用编译器编译为字节码(Bytecode)，  
    编译过程中会同时产生智能合约的二进制接口规范 (Application Binary Interface，简称为 ABI);
2. 通过交易(Transaction)的方式将字节码部署到以太坊网络，每次成功部署都会产生一个新的智能合约账户;
3. 使用Javascript编写的DApp通常通过 web3.js + ABI去调用智能合约中的函数来实现数据的读取和修改。

## 4.Solidity编译器

### Remix

* Remix是一个基于Web浏览器的Solidity IDE;可在线使用而无需安装任何东西
* <http://remix.ethereum.org>

### Replit

* <https://replit.com/>

### solcjs

* solc是Solidity 源码库的构建目标之一，它是Solidity的命令行编译器
* 使用npm可以便捷地安装Solidity编译器solcjs
* npm install -g solc

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/solidity/eth_solidity_compile.png)  
![eth_solidity_compile](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/solidity/eth_solidity_compile.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
