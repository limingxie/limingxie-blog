---
author: "li_mingxie"
title: "【solidity】bytes固定长度和动态长度(03)"
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

这篇文章简单的整理了`bytes固定长度和动态长度(03)`类型。  <!--more-->  

## 1.bytes固定长度

```sol
pragma solidity ^0.4.0;

contract ByteArray {
    //bytes1,bytes2,bytes3... bytes32
    bytes1 public num1 = 0x7a; //0111 1010
    bytes2 public num2 = 0x7a68; //0111 1010 0110 1000
    bytes3 public num3 = 0x7a68; //0111 1010 0110 1000.  返回 0x007a68

    function getLength() view public returns(uint) {
        return num1.length; // 1
    }

    function getLength2() view public returns(uint) {
        return num2.length; // 2
    }

    function getLength3() view public returns(uint) {
        return num3.length; // 3
    }

    function bijiao() view public returns(bool) {
        return num1 < num2; // true
    }

    function bijiao2() view public returns(bool) {
        return num2 == num3; // false
    }

    function bijiao3() view public returns(bool) {
        return num2 > num3; // true
    }

    bytes1 a = 0x7a; //0111 1010
    bytes1 b = 0x68; //0110 1000

    function weiyu() view public returns(bytes1) {
        return a & b;// 0x68
    }
    function weihuo() view public returns(bytes1) {
        return a | b;// 0x7a
    }
    function weifan() view public returns(bytes1) {
        return ~a; // 0x85
    }
    function weiyihuo() view public returns(bytes1) {
        return a ^ b;// 0x12
    }
    function zuoyi() view public returns(bytes1) {
        return a << 1; // 0xf4
    }
    function youyi() view public returns(bytes1) {
        return a >> 1; // 0x3d
    }

    bytes2 aa = 0x7a68;
    // bytyes 初始化后，不能修改单租数据
    function changeContent() public {
        //aa[1] = 2; //编译报错
        aa = 0x7a3d; //替换值是可以
    }
}
```

#### 2进制转16进制

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_09.png)  
![solidity_09](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_09.png)

#### 16进制bytes的位符操作

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_09.png)  
![solidity_09](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_09.png)

## 2.bytes动态长度

```sol
pragma solidity ^0.4.0;

contract DynamicByte{
    bytes public name = new bytes(2);

    function initName() public {
        name[0] = 0x7a;
        name[1] = 0x68;
    }

    function getLength() view public returns(uint){
        return name.length;
    }

    function changeName() public {
        name[0] = 0x88;
    }

    function changeLength() public {
        name.length = 5; //位置补0
    }

    function pushTest() public {
        name.push(0x99); // 加到后面的位置，如果长度为2，长度会变成3
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
