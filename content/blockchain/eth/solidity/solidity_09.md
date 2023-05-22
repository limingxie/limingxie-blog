---
author: "li_mingxie"
title: "【solidity】modifire(09)"
date: 2023-05-09T23:28:49+08:00
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

这篇文章简单的整理了`modifire`相关的内容。  <!--more-->  

## 1.基本用法

可以把共同的验证代码写成modifier

```sol
pragma solidity ^0.4.0;

contract modifierTest {

    address public owner;
    uint public num = 0;

    constructor() public {
        owner = msg.sender;
    }

    // 定义modifier
    modifier OnlyOwener{
        require(msg.sender == owner);

        _; // 这里添加自定义代码
    }

    // 把代码嵌入到OnlyOwener modifier 的 _; 处。 
    function changeIt(uint _num) public OnlyOwener {
        num = _num;
    }
}
```

## 2.modifier的参数

```sol
pragma solidity ^0.4.0;

contract modifierTest {

    uint public level = 0;
    string public name;
    uint public DNA;

    function changeLevel(uint _level) public {
        level =_level;
    }

    // 普通的验证
    function changeName(string _name) public {
        require(level >=2 );
        name = _name;
    }
    // 普通的验证
    function changeDNA(uint _dna) public {
        require(level >=10);
        DNA = _dna;
    }

    // modifier的参数
    modifier controllevel(uint needLevel) {
        require(level >= needLevel);
        _;
    }

    // 使用有参数的modifier
    function changeName1(string _name) public controllevel(2) {
        // require(level >= 2 );
        name = _name;
    }
    // 使用有参数的modifier
    function changeDNA1(uint _dna) public controllevel(10) {
        // require(level >= 10);
        DNA = _dna;
    }
}
```

## 3.多个modifier的执行顺序

```sol
pragma solidity ^0.4.0;

contract multiModifierTest3 {

    uint public a = 0;

    // 定义modifier
    modifier modifier1{
        a=1;

        _; 

        a=2;
    }

    modifier modifier2{
        a=3;

        _; 

        a=4;
    }

    // 多个modifier
    // 执行顺序 a=1 a=3 a=10 a=4 a=2
    // modifier1的 _; 执行的是 modifier2; modifier2的 _; 彩之星test的方法
    function test() public modifier1 modifier2 {
        a = 100;
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
