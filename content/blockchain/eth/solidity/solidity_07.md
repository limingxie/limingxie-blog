---
author: "li_mingxie"
title: "【solidity】函数-重载，参数，返回值(07)"
date: 2023-05-07T23:28:49+08:00
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

这篇文章简单的整理了`函数-重载，参数，返回值`相关的内容。  <!--more-->  

## 1.重载

函数名一样，参数类型或数量不同

```sol
pragma solidity ^0.4.0;

contract chongzai {
    function fun1() {}
    function fun1(uint num) {}

    function fun2(uint num) {}
    function fun2(uint8 num) {}

    function fun3(address addr) {}
    function fun3(uint160 addr) {}

    uint public a = 0;
    function test1(uint num) {
        a=100;
    }
    function test1(uint8 num) {
        a=200;
    }
    function test() {
        test1(1); //编译报错，因为这个1满足uint和uint8类型
        test1(256); //超过unit8的范围，就不会报错
    }

    //这两个是相同的类型，所以编译报错
    function test2(address addr)  {}
    function test2(uint160 addr) {}
}
```

## 2.函数的命名参数

```sol
pragma solidity ^0.4.0;

contract funParam {
    
    uint public num;

    string public name;

    function setParam(uint _num, string _name){
        num = _num;
        name = _name;
    }

    function Test() {
        setParam(10, "limingxie");
    }

    function Test1() {
        setParam({_num:100, _name:"mingxie"});
    }

}
```

## 3.返回值

```sol
pragma solidity ^0.4.0;

contract funreturn {
    function returnTest() public returns(uint num) {
        uint a = 10;
        return a;
    }

    // 这么写也可以返回 num
    function returnTest2() public returns(uint num) {
        num = 100;
    }

    function returnTest3() public returns(uint num) {
        uint a = 10;
        num = 100;
        return a;
    }

    function returnTest4()  public returns(uint num) {
        uint a = 10;
        num = 100;
        return 1;
    }

    function returnTest5()  public returns(uint num, uint unm2) {
        return (10,20);
    }
}

```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
