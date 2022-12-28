---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity关键字(06)"
date: 2022-11-27T23:28:49+08:00
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


这篇文章简单的整理了`Solidity关键字`。  <!--more-->  

## 1.Solidity源文件布局

### pragma (版本杂注)

* 源文件可以被版本杂注pragma所注解，表明要求的编译器版本
* 例如:pragma solidity ^0.4.0;
* 源文件将既不允许低于 0.4.0 版本的编译器编译，  
    也不允许高于(包含)0.5.0 版本的编译器编译(第二个条件因使用 ^ 被添加)

### import(导入其它源文件)

* Solidity所支持的导入语句import，语法同 JavaScript(从 ES6 起)非常类似

## 2.Solidity源文件布局-- import

**import "filename"**  
从“filename”中导入所有的全局符号到当前全局作用域中

**import \* as symbolName from "filename"**  
创建一个新的全局符号symbolName，其成员均来自 “filename” 中全局符号

**import {symbol1 as alias, symbol2} from "filename"**  
创建新的全局符号alias和symbol2，分别从"filename"引用symbol1和symbol2

**import "filename" as symbolName**  
这条语句等同于 import* as symbolName from "filename";

## 3.Solidity值类型

#### 布尔(bool)

可能的取值为字符常量值 true 或 false

#### 整型(int/uint)

分别表示有符号和无符号的不同位数的整型变量;
支持 关键字 uint8 到 uint256(无符号，从 8 位到 256 位)以及int8到int256， 以8位为步长递增

#### 定长浮点型(fixed / ufixed)

表示各种大小的有符号和无符号的定长浮 点型;  
在关键字ufixedMxN和fixedMxN中，M表示该类型占用的位数，N表示可用的小数位数

#### 地址(address)

存储一个 20 字节的值(以太坊地址大小)

#### 定长字节数组

关键字有 bytes1， bytes2， bytes3， ...， bytes32

#### 枚举(enum)

一种用户可以定义类型的方法，与C语言类似，默认从0开始递增，一般用来模拟合约的状态

#### 函数(function)

一种表示函数的类型

## 4.Solidity引用类型

#### 数组(Array)

* 数组可以在声明时指定长度(定长数组)，也可以动态调整大小(变长数组、动态数组)  
* 对于存储型(storage)的数组来说，元素类型可以是任意的(即元素也可以是数组类型，映射类型或者结构体);  
    对于内存型 (memory)的数组来说，元素类型不能是映射(mapping)类型

#### 结构(Struct)

* Solidity 支持通过构造结构体的形式定义新的类型

#### 映射(Mapping)

• 映射可以视作哈希表，在实际的初始化过程中创建每个可能的key，并将其映射到字节形式全是零的值(类型默认值)

## 5. Solidity地址类型

#### address

地址类型存储一个20 字节的值(以太坊地址的大小);  
地址类型也有成员 变量，并作为所有合约的基础

#### address payable(v0.5.0引入)

• 与地址类型基本相同，不过多出了 transfer 和 send 两个成员变量

#### 两者区别和转换

* Payable 地址是可以发送ether的地址，而普通address不能
* 允许从payable address到address的隐式转换，  
    而反过来的直接转换是不可能的(唯一方法是通过uint160来进行中间转换)
* 从0.5.0版本起，合约不再是从地址类型派生而来，  
    但如果它有payable的回退函数，那同样可以显式转换为address或者address payable类型

### 地址类型成员变量

#### \<address>.balance (uint256)

该地址的ether余额，以Wei为单位

#### \<address payable>.transfer(uint256 amount)

向指定地址发送数量为amount的ether(以Wei为单位)，  
失败时抛出异常，发送2300gas的矿工费，不可调节

#### \<address payable>.send(uint256 amount) returns (bool)

向指定地址发送数量为amount的ether(以Wei为单位)，  
失败时返回false，发送2300gas的矿工费用，不可调节

#### \<address>.call(bytes memory) returns (bool, bytes memory)

发出底层函数 CALL，失败时返回 false，发送所有可用 gas，可调节

#### \<address>.delegatecall(bytes memory) returns (bool, bytes memory)

发出底层函数 DELEGATECALL，失败时返回 false，发送所有可用 gas，可调节

#### \<address>.staticcall(bytes memory) returns (bool, bytes memory)

发出底层函数 STATICCALL ，失败时返回 false，发送所有可用 gas，可调节

### 地址成员变量用法

#### balance 和 transfer

可以使用balance属性来查询一个地址的余额，  
可以使用transfer函数向一个payable地址发送以太币Ether(以wei为单位)

```sol
address payable x = address(0x123);
address myAddress = address(this);
if (x.balance < 10 && myAddress.balance >= 10) x.transfer(10);
```

#### send

send是transfer的低级版本。如果执行失败，当前的合约不会因为异常而终止，**`但send会返回false`**

#### call

也可以用call来实现转币的操作，通过添加.gas()和.value()修饰器:

```sol
nameReg.call.gas(1000000).value(1ether)(abi.encodeWithSignature("register(string)", "MyName"));
```

## 6.字符数组(Byte Arrays)

#### 定长字符数组

* 属于值类型，bytes1，bytes2，...，bytes32分别代表了长度为1到32的字节序列
* 有一个.length属性，返回数组长度(只读)

#### 变长字符数组

属于引用类型，包括 bytes和string，不同的是bytes是Hex字符串，而string是UTF-8编码的字符串

## 7.枚举(Enum)

* 枚举类型用来用户自定义一组常量值
* 与C语言的枚举类型非常相似，对应整型值

```sol
pragma solidity >=0.4.0 <0.6.0; 
contract Purchase {
    enum State { Created, Locked, Inactive } 
}
```

## 8.数组(Array)

* 固定大小k和元素类型T的数组被写为T[k]，动态大小的数组为T[]。  
    例如，一个由5个uint动态数组组成的数组是uint[][5]
* 要访问第三个动态数组中的第二个uint，可以使用x[2][1]
* 越界访问数组，会导致调用失败回退
* 如果要添加新元素，则必须使用.push()或将.length增大
* 变长的storage数组和bytes(不包括string)有一个push()方法。  
    可以将一个新元素附加到数组末端，返回值为当前长度

```sol
pragma solidity >=0.4.16 <0.6.0; 
contract C {
    function f(uint len) public pure {
        uint[] memory a = new uint[](7);
        bytes memory b = new bytes(len); 
        assert(a.length == 7); 
        assert(b.length == len);
        a[6] = 8;
    } 
}
```

## 9.结构(Struct)

* 结构类型可以在映射和数组中使用，它们本身可以包含映射和数组。
* 结构不能包含自己类型的成员，但可以作为自己数组成员的类型，也可以作为自己映射成员的值类型

```sol
pragma solidity >=0.4.0 <0.6.0; 
contract Ballot {
    struct Voter {
        uint weight;
        bool voted;
        uint vote; 
    }
}
```

## 10.映射(Mapping)

* 声明一个映射:mapping(_KeyType =>_ValueType)
* _KeyType可以是任何基本类型。这意味着它可以是任何内置值类型加上字节和字符串。  
    不允许使用用户定义的或复杂的类型，如枚举，映射，结构以及除bytes和string之外的任何数组类型。
* _ValueType可以是任何类型，包括映射。

```sol
pragma solidity >=0.4.0 <0.6.0;
contract MappingExample { 
    mapping(address => uint) public balances; 
    function update(uint newBalance) public {
        balances[msg.sender] = newBalance;
    }
}
contract MappingUser {
    function f() public returns (uint) {
        MappingExample m = new MappingExample();
        m.update(100);
        return m.balances(address(this));
    }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
