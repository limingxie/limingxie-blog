---
author: "li_mingxie"
title: "【solidity】枚举enum(16)"
date: 2023-05-16T23:28:49+08:00
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

这篇文章简单的整理了`枚举enum`相关的内容。  <!--more-->  

```sol
pragma solidity ^0.4.0;

contract enumTest {

    // enum jiaju{}; 空值会报错，必须要有值
    // 不能有汉字
    // uint shafa = 2; 同一个效果
    enum jiaju{yizi,zhuozi,shafa}

    jiaju dataJiaju = jiaju.zhuozi;

    function getEnum() pure public returns(jiaju){
        return jiaju.shafa; //
    }

    function test() public returns(jiaju){
        require(dataJiaju == jiaju.zhuozi);
        dataJiaju = jiaju.shafa;
        return dataJiaju;
    }

    function test1() view public returns(string){
        require(dataJiaju == jiaju.shafa);
        return "结束";
    }

}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
