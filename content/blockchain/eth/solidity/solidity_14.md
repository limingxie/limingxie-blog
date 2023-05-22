---
author: "li_mingxie"
title: "【solidity】memory和storage(14)"
date: 2023-05-14T23:28:49+08:00
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

这篇文章简单的整理了`memory和storage`相关的内容。  <!--more-->  

## 1.memory和storage简单例子

```sol
pragma solidity ^0.4.0;

contract memoryTest {

    uint public num1 = 5;

    function add(uint num) pure public returns(uint) {
        num = num+1;
        return num;
    }

    function test() pure public returns(uint, uint){
        uint i =2;
        uint j = add(i);
        return(i,j); // i = 2, j = 3
    }

    function changeNum1() public {
        num1 = num1 + 1; // 只有这操作影响全局变量num1
        add(num1); // 这个没效果
    }
}
```

## 2.数组操作的特例

```sol
pragma solidity ^0.4.0;

contract memoryTest {

    uint[] public arrx;

    function test(uint[] arry) public returns(uint){
        arrx = arry;

        //当我们在函数内部定义了一个可变长度的数组时，他默认类型是storage类型
        //arrz 指向了区块链上的 arrx，所以修改arrz元素时，arrx也会搜到影响
        uint[] arrz = arrx;
        
        arrz[0]=100; //该操作也会影响arrx
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
