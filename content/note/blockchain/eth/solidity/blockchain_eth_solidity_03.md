---
author: "li_mingxie"
title: "【区块链笔记】ETH_Solidity简单代币合约(03)"
date: 2022-11-24T23:28:49+08:00
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


这篇文章简单的整理了`以太坊solidity简单代币合约`。  <!--more-->  

## 1.合约结构

* 状态变量(State Variables) 作为合约状态的一部分，值会永久保存在存储空间内。
* 函数(Functions) 合约中可执行的代码块。
* 函数修饰器(Function Modifiers) 用在函数声明中，用来补充修饰函数的语义。
* 事件(Events) 非常方便的EVM日志工具接口。

## 2.Coin例子

```sol
pragma solidity ^0.4.22;

contract Coin{
    address public minter;
    mapping (address=>uint) balances;
    event Sent(address from, address to, uint amount);
    constructor()public{
        minter = msg.sender;
    }
    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        balances[receiver] += amount;
    }
    function send(address receiver, uint amount) public {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        balances[receiver] += amount;

        emit Sent(msg.sender, receiver, amount);
    }
    function getMinterBalance() public view returns(uint) {
        return balances[minter];
    }
}
```

有造币者(minter)，造币方法(mint)，以及发放币的方法(send)  
只是为了测试，没有多余的错误和溢出判断。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/solidity/eth_solidity_003.png)  
![eth_solidity_003](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/solidity/eth_solidity_003.png)

## 2.Coin去掉造币方法，设定一个定量

```sol
pragma solidity ^0.4.22;

contract Coin{
    mapping (address=>uint) balances;
    // event Sent(address from, address to, uint amount);
    constructor(uint initTotalSupply) public {
       balances[msg.sender] = initTotalSupply
    }
    function send(address receiver, uint amount) public returns(bool success) {
        require(balances[msg.sender] >= amount);
        require(balances[receiver] + amount >= balances[receiver]);
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        return true;
        // emit Sent(msg.sender, receiver, amount);
    }
}
```

直接构造的时候设定一个定量的币，有发放币的方法。

[图片备用地址](https://limingxie.github.io/images/blockchain/ethereum/solidity/eth_solidity_004.png)  
![eth_solidity_004](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/ethereum/solidity/eth_solidity_004.png)

address public minter;

* 这一行声明了一个可以被公开访问的 address 类型的状态变量。
* 关键字 public 自动生成一个函数，允许你在这个合约之外 访问这个状态变量的当前值。

mapping(address => uint) public balances;

* 也创建一个公共状态变量，但它是一个更复杂的数据类型， 该类型将 address 映射为无符号整数。
* mappings 可以看作是一个哈希表，它会执行虚拟初始化， 把所有可能存在的键都映射到一个字节表示为全零的值。

event Sent(address from, address to, uint amount);

* 声明了一个“事件”(event)，它会在 send 函数的最后一行触发
* 用户可以监听区块链上正在发送的事件，而不会花费太多成本。一旦它被发出，监听该事件的listener都将收到通知
* 所有的事件都包含了 from ， to 和 amount 三个参数，可方便追踪事务

emit Sent(msg.sender, receiver, amount);

* 触发Sent事件，并将参数传入

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
