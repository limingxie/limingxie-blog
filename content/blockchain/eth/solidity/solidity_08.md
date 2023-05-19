---
author: "li_mingxie"
title: "【solidity】作用域和构造函数(08)"
date: 2023-05-08T23:28:49+08:00
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

这篇文章简单的整理了`函数`相关的内容。  <!--more-->  

## 1.作用域

```sol
pragma solidity ^0.4.0;

contract ValueCopy {

    uint public a = 100;
    uint b = 200;

    function test() view public returns(uint) {
        return a; // 返回 100
    }

    function test1() view public returns(uint) {
        // a不会报重定义错误，函数内部只认内部参数a
        uint a = 300;
        return a; // 返回 300
    }

    function test2(uint a) view public returns(uint) {
        a = 400;

        // a重定义报错
        // for(uint a = 0; a < 10: a++){

        // }
        {
            // a重定义报错
            // uint a = 500;  
        }

        return a; // 返回 400
    }
}
```

## 2.构造函数

```sol
pragma solidity ^0.4.0;

contract gouzao {
    uint public aa = 100;
    constructor(uint _aa) public{
        aa = _aa;
    }
}

contract gouzao2 {
    address public owner;
    constructor() public{
        // 可以记录合约的拥有者
        owner = msg.sender;
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
