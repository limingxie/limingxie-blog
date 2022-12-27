---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity简单例子(02)"
date: 2022-11-23T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---


这篇文章简单的整理了`以太坊solidity简单例子`。  <!--more-->  

## 1.Solidity中合约

一组代码(合约的函数)和数据(合约的状态)，它们位于以太坊区块链的一个特定地址上

* 代码行uint storedData; 声明一个类型为uint(256位无符号整数)的状态变量，叫做storedData
* 函数 set和 et可以用来变更或取出变量的值

## 2.SimpleStorage

```sol
pragma solidity ^0.8.7;

contract SimpleStorage{
    uint myData;
    function setData(uint newData) public {
        myData = newData;
    }
    function getData() public view returns(uint){
        return myData;
    }
    function pureAddAndSub(uint a, uint b) public pure returns(uint sum, uint diff){
        return (a+b,a-b);
    }
}
```

简单的写了一个set和get方法，以及添加了求和，求差的方法。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/solidity/eth_solidity_001.png)  
![eth_solidity_001](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/solidity/eth_solidity_001.png)

## 3.Car

```sol
pragma solidity ^0.4.22;

contract Car{
    string brand;
    uint public price;
    constructor(string initBrand , uint initPrice) public {
        brand = initBrand;
        price = initPrice;
    }
    function setBrand(string newBrand) public {
        brand = newBrand;
    }
    function getBrand() public view returns(string) {
        return brand;
    }
    function setPrice(uint newPrice) public {
        price = newPrice;
    }
}
```

constructor可以在发布的时候直接赋值。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/solidity/eth_solidity_002.png)  
![eth_solidity_002](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/solidity/eth_solidity_002.png)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
