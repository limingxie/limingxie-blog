---
author: "li_mingxie"
title: "【solidity】析构函数(13)"
date: 2023-05-13T23:28:49+08:00
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

这篇文章简单的整理了`析构函数`相关的内容。  <!--more-->  

## 1.销毁合约

```sol
pragma solidity ^0.4.0;

contract destruct {

    address owner;
    uint public money = 0;

    constructor(){
        owner = msg.sender;
    }

    function increment(){
        money += 10;
    }

    // 销毁当前的合约
    function kill(){
        if (msg.sender == owner) {
            selfdestruct(owner);
        }
    }

}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
