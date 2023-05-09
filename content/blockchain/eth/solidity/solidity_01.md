---
author: "li_mingxie"
title: "【solidity】helloWorld_boolean(01)"
date: 2023-05-01T23:28:49+08:00
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

这篇文章简单的整理了`solidity的HelloWorld和Boolean`类型。  <!--more-->  

## 1.HelloWorld

先看solidity的一段代码

```sol
pragma solidity ^0.4.0;

contract HelloWorld {
    string MyName = "testName";
    
    function getName() public view returns(string){
        return MyName;
    }

    function changeName(string _newName) public {
        MyName = _newName;
    }

    function puerTest(string _name) pure public returns(string) {
        return _name;
    }
}
```

下一步编译  
[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_01.png)  
![solidity_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_01.png)

下一步执行  
[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_02.png)  
![solidity_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_02.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_03.png)  
![solidity_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_03.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_04.png)  
![solidity_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_04.png)

当每次执行changeName时以太币会减少  
[图片备用地址](https://limingxie.github.io/images/blockchain/solidity/solidity_05.png)  
![solidity_05](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/solidity/solidity_05.png)

## 2.Boolean

```sol
pragma solidity ^0.4.0;

contract BooleanTest{
    bool _a;
    int num1 = 100;
    int num2 = 200;

    function getBool() public view returns(bool) {
        return _a;//false
    }

    function getBool2() public view returns(bool){
        return !_a;//true
    }

    function panduan() public view returns(bool){
        return num1==num2;//false
    }

    function paduan2() public view returns(bool){
        return num1!=num2;//true
    }

    function yu() public view returns(bool) {
        return (num1==num2) && true;//false
    }

    function yu2() public view returns(bool) {
        return (num1!=num2) && true; //true
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
