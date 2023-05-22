---
author: "li_mingxie"
title: "【solidity】继承重载和多重继承(12)"
date: 2023-05-12T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "eth",
    "solidity",
]
---

这篇文章简单的整理了`继承重载和多重继承`相关的内容。  <!--more-->  

## 1.继承重载

```sol
pragma solidity ^0.4.0;

contract father {
    uint public money = 10000;

    function dahan() pure public returns(string) {
        return "dahan-father";
    }

}

contract son is father {

    uint public money = 99;
    function getMoney() view public returns(uint){
        return money; // 99 继承重载
    }

    // 函数继承重载
    function dahan() pure public returns(string) {
        return "dahan-son";
    }

    function testDahan() pure public returns(string) {
        return dahan(); // dahan-son
    }

}
```

## 2.多重继承

```sol
pragma solidity ^0.4.0;

contract father {
    uint public money = 10000;
    uint public height = 180;

    function returnAB() pure public returns(string){
        return "A";
    }
}

contract mother {
    uint public height = 150;

    function returnAB() pure public returns(string){
        return "B";
    }
}

contract son is father, mother {

    // uint public height = 170; 自己有的属性会覆盖继承过来的属性

    // 同一个属性继承，后面的属性会覆盖前面的属性
    function getHeight() view public returns(uint){
        return height; // 150
    }

    // 同一个function继承，后面的属性会覆盖前面
    function test() pure public returns(string){
        return returnAB(); // B
    }

}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
