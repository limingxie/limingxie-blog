---
author: "li_mingxie"
title: "【solidity】继承和调用权限(10)"
date: 2023-05-10T23:28:49+08:00
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

这篇文章简单的整理了`继承和调用权限`相关的内容。  <!--more-->  

## 1.基本用法

```sol
pragma solidity ^0.4.0;

contract father {
    uint public money = 10000;

    function dahan() pure public returns(string) {
        return "dahan";
    }

}

contract son is father {

    function getMoney() view public returns(uint){
        return money; // 10000
    }

    function test() pure public returns(string) {
        return dahan(); // "dahan"
    }
}
```

## 2.连续继承

grandfather <- father <- son  

```sol
pragma solidity ^0.4.0;

contract grandfather {
    uint public gudong = 200;

    function zhongdi() pure public returns(string){
        return "zhongdi";
    }
}

contract father is grandfather {
    uint public money = 10000;

    function dahan() pure public returns(string) {
        return "dahan";
    }

}

contract son is father {
    function getMoney() view public returns(uint){
        return money;
    }

    function test() pure public returns(string) {
        return dahan();
    }

    function getGudong() view public returns(uint){
        return gudong;
    }

}
```

## 3.public internal external private

```sol
pragma solidity ^0.4.0;

contract father {

    uint public money = 10000;

    // uint money = 10000;  不加上任何属性符 可以继承
    // uint internal money = 10000;  可以被继承

    // uint external money = 10000; 不能使用该符号
    // uint private money = 10000; 不能被继承


    // 不加符号，public, internal 可以被继承  external 必须要 this.danhan
    // private 不能被继承
    function dahan() pure public returns(string) {
        return "dahan";
    }

    // public 任何情况都可以调用
    // internal 只能是合约的内部调用 或是 继承合约的内部调用
    // external 只能是合约的外部调用 或是 继承合约的外部调用 
    //          可以用this.danhan() 这种方式在内部调用。因为this表示该合约，也是表示外部调用
    // private 只能是合约自己调用

}

contract son is father {

    function getMoney() view public returns(uint){
        return money;
    }

}

contract externalTest {
    // 创建合约以后，调用方法。 跟继承不一样的方式调用
    father f = new father();

    function externalTestIt() view public returns(string){
        return f.dahan();
    }
}
```

## 4.view pure payable

view和pure不会产生gas费用  

`view`修饰符表示函数不会修改合约的状态，也不会向区块链发送任何交易。  
它只是读取合约的状态并返回结果。  

```sol
function getBalance() public view returns (uint) {
    return address(this).balance;
}
```

`pure`修饰符表示函数不会读取或修改合约的状态，也不会向区块链发送任何交易。  
它只是执行计算并返回结果。  

```sol
function add(uint a, uint b) public pure returns (uint) {
    return a + b;
}
```

`payable` 表示函数可以接收以太币作为参数，并将其存储到合约中。  
在调用 payable 函数时，需要支付一定的 gas 费用，以便将以太币存储到合约中。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
