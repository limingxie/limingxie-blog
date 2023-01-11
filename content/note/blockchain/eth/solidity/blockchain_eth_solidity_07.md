---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity数据位置(07)"
date: 2022-11-28T23:28:49+08:00
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


这篇文章简单的整理了`Solidity数据位置`。  <!--more-->  

## 1.Solidity数据位置

所有的复杂类型，即数组、结构和映射类型，都有一个额外属性，“数据位置”，用来说明数据是保存在内存memory中还是存储storage中

* 根据上下文不同，大多数时候数据有默认的位置，但也可以通过在类型名后增加关键字 storage 或 memory 进行修改
* 函数参数(包括返回的参数)的数据位置默认是memory，局部变量的数据位置默认是 storage，状态变量的数据位置强制是 storage
* 另外还存在第三种数据位置，calldata，这是一块只读的，且不会永久存储的位置，用来存储函数参数。  
    外部函数的参数(非返回参数) 的数据位置被强制指定为calldata ，效果跟 memory 差不多

## 2.数据位置整理

#### 强制指定的数据位置

* 外部函数的参数(不包括返回参数):calldata;
* 状态变量:storage

#### 默认数据位置

* 函数参数(包括返回参数):memory;
* 引用类型的局部变量:storage
* 值类型的局部变量:栈(stack)

#### 特别要求

公开可见(publiclyvisible)的函数参数一定是memory类型，  
如果要求是 storage 类型 则必须是 private 或者 internal 函数，这是为了 防止随意的公开调用占用资源  

## 3.注意事项(例子)

#### 例子1

```sol
pragma solidity >=0.4.0 <0.5.0;
contract C {
    uint public a;
    uint public b;
    uint[] public data;
    function fn() public {
        uint[] x;
        x.push(2);
        data = x;
    }
}
```

这里每次调用fn的方法的时候a值会自动+1;  
因为在函数里声明局部变量storage引用类型，赋初始值的话，他会默认指向第一个值。  
uint[] x => a ;  
而且每次调用的时候x的length会赋给a，所以每次push一次会自动+1；  

修改后 ==>  

```sol
pragma solidity >=0.4.0 <0.5.0;
contract C {
    uint public a;
    uint public b;
    uint[] public data;
    function fn() public {
        uint[] x = data;
        x.push(2);
    }
}
```

#### 例子2

```sol
pragma solidity ^0.4.0; 
    contract C {
    uint[] x;
    function f(uint[] memoryArray) public {
        x = memoryArray; 
        uint[] y = x;
        y[7];
        y.length = 2; 
        delete x;
        y = memoryArray; // 这里会报错，因为memoryArray是引用值，不能直接赋给storage类型的引用值。 值的话可以直接拷贝
        delete y;
        g(x);
        h(x);
    }
    function g(uint[] storage storageArray) internal {} 
    function h(uint[] memoryArray) public {} 
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
