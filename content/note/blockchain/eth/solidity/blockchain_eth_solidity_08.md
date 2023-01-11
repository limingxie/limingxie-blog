---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity函数(08)"
date: 2022-11-29T23:28:49+08:00
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


这篇文章简单的整理了`Solidity函数`相关的内容。  <!--more-->  

## 1.Solidity函数声明和类型

函数的值类型有两类:- 内部(internal)函数和外部(external) 函数  

* 内部函数只能在当前合约内被调用(更具体来说，在当前代码块内，包括内部库函数和继承的函数中)，  
    因为它们不能在当前合约上下文的外部被执行。  
    调用一个内部函数是通过跳转到它的入口标签来实现的，就像在当前合约的 内部调用一个函数。
* 外部函数由一个地址和一个函数签名组成，可以通过外部函数调用传递或者 返回
* 调用内部函数:直接使用名字 f
* 调用外部函数:this.f(当前合约)，a.f(外部合约)

## 2.Solidity函数可见性

函数的可见性可以指定为 external，public ，internal 或者 private;  
对于状态变量，不能设置为 external ，默认是 internal。  

* external:外部函数作为合约接口的一部分，意味着我们可以从其他合 约和交易中调用。  
    一个外部函数 f不能从内部调用(即 f 不起作用，但this.f()可以)。  
    当收到大量数据的时候，外部函数有时候会更有效 率。
* public:public 函数是合约接口的一部分，可以在内部或通过消息调用。  
    对于public状态变量，会自动生成一个getter函数。  
* internal:这些函数和状态变量只能是内部访问(即从当前合约内部或从它派生的合约访问)，不使用this调用。  
* private:private函数和状态变量仅在当前定义它们的合约中使用，并 且不能被派生合约使用  

```sol
pragma solidity >=0.4.0 <0.6.0;

contract C {
    uint256 private data;

    function f(uint256 a) private pure returns (uint256 b) {
        return a + 1;
    }
    function setData(uint256 a) public {
        data = a;
    }
    function getData() public view returns (uint256) {
        return data;
    }
    function compute(uint256 a, uint256 b) internal pure returns (uint256) {
        return a + b;
    }
}

contract D {
    function readData() public {
        C c = new C();
        uint256 local = c.f(7); //private类型是不能这么调用，会报错
        c.setData(3);
        local = c.getData();
        local = c.compute(3, 5); //internal类型是需要继承后才能调用
    }
}

contract E is C {
    function g() public {
        C c = new C();
        uint256 val = c.compute(3, 5); //继承的是直接调用应该写成 c.compute(3, 5) => compute(3, 5)
    }
}
```

```sol
pragma solidity >=0.4.16 <0.6.0;

contract C {
    function f(uint256 a) private pure returns (uint256 b) {
        return a + 1;
    }

    function setData(uint256 a) internal {
        data = a;
    }

    uint256 public data;

    function x() public {
        data = 3; // 内部访问
        uint256 val = this.data(); // 外部访问 uint val2 = f(data);
    }
}
```

## 3.Solidity函数状态可变性

* pure:纯函数，不允许修改或访问状态
* view:不允许修改状态
* payable:允许从消息调用中接收以太币Ether。
* constant:与view相同，一般只修饰状态变量，不允许赋值 (除初始化以外)

#### 以下情况被认为是修改状态

* 修改状态变量
* 产生事件。
* 创建其它合约。
* 使用selfdestruct。
* 通过调用发送以太币。
* 调用任何没有标记为view或者pure的函数。
* 使用低级调用。
* 使用包含特定操作码的内联汇编。

#### 以下被认为是从状态中进行读取

* 读取状态变量。
* 访问 this.balance 或者 \<address>.balance。
* 访问 block，tx，msg中任意成员 (除 msg.sig 和 msg.data 之外)。
* 调用任何未标记为pure的函数。
* 使用包含某些操作码的内联汇编。

## 4.函数修饰器(modifier)

* 使用修饰器modifier可以轻松改变函数的行为。  
    例如，它们可以在执行函数之前自动检查某个条件。  
    修饰器modifier是合约的可继承属性，并可能被派生合约覆盖
* 如果同一个函数有多个修饰器modifier，它们之间以空格隔开，修饰 器modifier会依次检查执行。

```sol
pragma solidity >=0.4.22 <0.6.0; 
contract Purchase {
    address public seller;
    modifier onlySeller() { // Modifier
        require( msg.sender == seller, "Only seller can call." );
        _; 
    }

    function abort() public view onlySeller returns(uint) { // Modifier usage
        return 200;
    }
}
```

## 5.回退函数(fallback)

* 回退函数(fallback function)是合约中的特殊函数;  
    没有名字，不能有参数也不能有返回值
* 如果在一个到合约的调用中，没有其他函数与给定的函数标识符匹配(或没有提供调用数据)，  
    那么这个函数(fallback 函数)会被执行
* 每当合约收到以太币(没有任何数据)，回退函数就会执行。  
    此外，为了接收以太币，fallback 函数必须标记为 payable。  
    如果不存在这 样的函数，则合约不能通过常规交易接收以太币
* 在上下文中通常只有很少的gas可以用来完成回退函数的调用，所以使fallback函数的调用尽量廉价很重要

```sol
pragma solidity >0.4.99 <0.6.0;

contract Sink {
    function() external payable {}
}
contract Test {
    function() external { x = 1; }
    uint256 x;
}
contract Caller {
    function callTest(Test test) public returns (bool) {
        (bool success, ) = address(test).call(
            abi.encodeWithSignature("nonExistingFunction()")
        );
        require(success);
        address payable testPayable = address(uint160(address(test)));
        return testPayable.send(2 ether);
    }
}
```

## 6.事件(event)

* 事件是以太坊EVM提供的一种日志基础设施。  
    事件可以用来做操作记录，存储为日志。  
    也可以用来实现一些交互功能，比如通知UI，返回函数调用结果等
* 当定义的事件触发时，我们可以将事件存储到EVM的交易日志中，日志是区块链中的一种特殊数据结构;  
    日志与合约关联，与合约的存储合并存入区块链中;  
    只要某个区块可以访问，其相关的日志就可以访 问;  
    但在合约中，我们不能直接访问日志和事件数据
* 可以通过日志实现简单支付验证SPV(SimplifiedPayment Verification)，  
    如果一个外部实体提供了一个带有这种证明的合约，  
    它可以检查日志是否真实存在于区块链中

## 7.Solidity异常处理

* Solidity使用“状态恢复异常”来处理异常。  
    这样的异常将撤消对当前调用(及其所有子调用)中的状态所做的所有更改，并且向 调用者返回错误。
* 函数assert和require可用于判断条件，并在不满足条件时抛出异 常
* assert()一般只应用于测试内部错误，并检查常量
* require() 应用于确保满足有效条件(如输入或合约状态变量)，或验证调用外部合约的返回值
* revert()用于抛出异常，它可以标记一个错误并将当前调用回退

## 8. Solidity中的单位

#### 以太币(ether)

* 以太币 Ether 单位之间的换算就是在数字后边加上 wei、 finney、 szabo 或 ether 来实现的，  
    如果后面没有单位，缺 省为 Wei。例如 2 ether == 2000 finney 的逻辑判断值为 true

| Unit | Wei Value | Wei |
|---|---|---|
| wei | 1 | 1 wei |
| Kwei (babbage) | 1e3 wei | 1,000 |
| Mwei (lovelace) | 1e6 wei | 1,000,000 |
| Gwei (shannon) | 1e9 wei | 1,000,000,000 |
| microether (szabo) | 1e12 wei | 1,000,000,000,000 |
| milliether (finney) | 1e15 wei | 1,000,000,000,000,000 |
| ether | 1e18 wei | 1,000,000,000,000,000,000 |

#### 时间

秒是缺省时间单位，在时间单位之间，数字后面带
有 seconds、 minutes、 hours、 days、 weeks 和 years 的可以进 行换算，基本换算关系如下:

* 1 == 1 seconds
* 1 minutes == 60 seconds
* 1 days == 24 hours
* 1 weeks == 7 days
* 1 years == 365 days
* 1 hours == 60 minutes

这些后缀不能直接用在变量后边。  
如果想用时间单位(例如 days)来将输入变量 换算为时间，你可以用如下方式来完成:

```sol
function f(uint start, uint daysAfter) public {
    if (now >= start + daysAfter * 1 days) { // ... }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
