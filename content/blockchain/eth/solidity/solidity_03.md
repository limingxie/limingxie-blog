---
author: "li_mingxie"
title: "【solidity】int和uint(02)"
date: 2923-05-03T23:28:49+08:00
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

这篇文章简单的整理了`int和uint`类型。  <!--more-->  

## 1.int和uint

```sol
pragma solidity ^0.4.0;

contract Math{
    int num1 = 100; // 正负 int256
    uint numa = 4; // 只能是正数 uint256  uint8 最大 255
    uint numb = 2;

    function jia(uint a, uint b) pure public returns(uint){
        return a+b; // a=4 b=2 : 6
    }

    function jian(uint a, uint b) pure public returns(uint){
        return a-b; // a=4 b=2 : 2
    }

    function cheng(uint a, uint b) pure public returns(uint){
        return a*b; // a=4 b=2 : 8
    }

    function chu(uint a, uint b) pure public returns(uint){
        return a/b; // a=4 b=2 : 2
    }

    function yu(uint a, uint b) pure public returns(uint){
        return a%b; // a=5 b=3 : 2
    }

    function pingfang(uint a, uint b) pure public returns(uint){
        return a**b; // a=3 b=3 : 27
    }

    function weiyu(uint8 a, uint8 b) pure public returns(uint) {
        return a & b;// a=3 b=4 : 0
    }
    function weihuo(uint8 a, uint8 b) pure public returns(uint) {
        return a | b;// a=3 b=4 : 7
    }
    function weifan(uint8 a) pure public returns(uint) {
        return ~a; // a=3 : 252
    }
    function weiyihuo(uint8 a, uint8 b) pure public returns(uint) {
        return a ^ b;// a=3 b=4 : 7
    }
    function zuoyi(uint8 a) pure public returns(uint) {
        return a << 1; // a=3 : 6
    }
    function youyi(uint8 a) pure public returns(uint) {
        return a >> 1; // a=3 : 1
    }

    function flow() pure public returns(uint){
        uint8 mm = 255;
        mm++;
        return mm; // 0
    }

    function flow2() pure public returns(uint){
        uint8 mm = 0;
        mm--;
        return mm; // 255
    }

    function errTest() pure public returns (int){
        int a = 2;
        int b = 0;
        return a/b; // 报错
    }

    function errTest2() pure public returns (int){
        int a = 2;
        int b = -1;
        return a << b;  // 报错
    }

    function intTest() pure public returns(uint){
        uint num = (2**800+1)-2**800;
        return num; // 1
    }


    function intTest2() pure public returns(uint){
        uint a = 2;
        uint b = 4;
        uint num = a/b;
        return num; // 0
    }

    function intTest3() pure public returns(uint){
        uint a = 2;
        uint b = 4;
        uint num = a/b*10000;
        return num; // 0
    }

    function intTest4() pure public returns(uint){
        uint num = 2/4*10000;
        return num; // 5000
    }

    function intTest5() pure public returns(uint8){
        uint8 num = 11111111111111111111111111111111111111111111111111 -11111111111111111111111111111111111111111111111110;
        return num; // 1
    }
}
```

## 2.位移符号

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_06.png)  
![solidity_06](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_06.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_07.png)  
![solidity_07](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_07.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
