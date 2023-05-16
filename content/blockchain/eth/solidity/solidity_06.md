---
author: "li_mingxie"
title: "【solidity】address和payable(06)"
date: 2023-05-06T23:28:49+08:00
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

这篇文章简单的整理了`address类型和payable`相关的内容。  <!--more-->  

## 1.address类型和uint之间的转换

```sol
pragma solidity ^0.4.0;

contract addresstest {
    address public account = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4; 
    //0x5B38Da6a701c568545dCfcB03FcB875f56beddC4  40  uint160
    //0xd9145CCE52D386f254917e481eB44e9943F39138  40  uint160

    function changeIt() view public returns(uint160){
        return uint160(account); //520786028573371803640530888255888666801131675076
    }

    function changeIt2() pure public returns(address){
        return address(520786028573371803640530888255888666801131675076); //0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
    }
} 
```

## 2.payable

```sol
pragma solidity ^0.4.0;

contract payableTest {

    // 通过 payable 给这个账户转账
    function pay() public payable {
        
    }

    // 获取账户上的金额
    function getBalance() constant public returns (uint256){
        return address(this).balance;  // 获取合约地址的余额
    }

    // this代表的是这个合约
    function getThis() view public returns(address) {
        return this;
    }

    // 获取address的余额
    function getAddreesBalance(address addr) view public returns(uint) {
        return addr.balance;
    }

    //给指定账户转账
    function transfer() public payable{
        address account = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;
        account.transfer(msg.value);
    }

    //给合约转账
    function transfer2() public payable{
        address(this).transfer(msg.value);
    }

    // 回滚函数
    function() public payable{
    }
}
```

## 3.全局变量

```sol
pragma solidity ^0.4.0;

contract grobal {

    // 合约的调用者
    function getgrobal() returns(address) {
        return msg.sender;
    }

    //难度
    function getgrobal2() returns(uint) {
        return block.difficulty;
    }

    //当前区块
    function getgrobal3() returns(uint) {
        return block.number;
    }

    //当前矿工
     function getgrobal4() returns(address) {
        return block.coinbase;
    }
}
```

```bash
block.blockhash(uint blockNumber) returns (bytes32)：指定区块的区块哈希——仅可用于最新的 256 个区块且不包括当前区块；而 blocks 从 0.4.22 版本开始已经不推荐使用，由 blockhash(uint blockNumber) 代替
block.coinbase (address): 挖出当前区块的矿工地址
block.difficulty (uint): 当前区块难度
block.gaslimit (uint): 当前区块 gas 限额
block.number (uint): 当前区块号
block.timestamp (uint): 自 unix epoch 起始当前区块以秒计的时间戳
gasleft() returns (uint256)：剩余的 gas
msg.data (bytes): 完整的 calldata
msg.gas (uint): 剩余 gas - 自 0.4.21 版本开始已经不推荐使用，由 gesleft() 代替
msg.sender (address): 消息发送者（当前调用）
msg.sig (bytes4): calldata 的前 4 字节（也就是函数标识符）
msg.value (uint): 随消息发送的 wei 的数量
now (uint): 目前区块时间戳（block.timestamp）
tx.gasprice (uint): 交易的 gas 价格
tx.origin (address): 交易发起者（完全的调用链）
```

## 4.账户之间转账

```sol
pragma solidity ^0.4.0;

contract payableTest {

    // 通过 payable 给这个合约账户转账
    function pay() public payable {
        
    }

    //给指定账户转账
    function transfer(address account) public payable{
        account.transfer(msg.value);
    }

    //给合约转账
    function transfer1(address account) public payable{
        account.transfer(2 ether);
    }

    //给合约转账
    function transfer2() public payable{
        address(this).transfer(2 ether);
    }

    function() public payable{
    }

    // 底层函数，返回值是bool类型，报错没有任何提示
    function sendMoney(address account) public payable{
       account.send(msg.value);  // 编译报错，建议使用transfer
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
