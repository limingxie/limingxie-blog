---
author: "li_mingxie"
title: "【solidity】字符string和bytes转换(04)"
date: 2923-05-04T23:28:49+08:00
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

这篇文章简单的整理了`string`相关的内容。  <!--more-->  

## 1.string

```sol
pragma solidity ^0.4.0;

contract DynamicString {
    string name = 'limingxie';

    function getLength () view public returns(uint) {
        // return name.length;// 报错没有这个方法
        return bytes(name).length; // 9
    }

    function changeName() public returns(bytes1) {
        // name[0]='l'; 报错 不能直接赋值
        bytes(name)[1] = 'l';
    }

    function getName() view public returns(bytes) {
        return bytes(name); // 0x6c6c6d696e67786965
    }

    string name2 = "!@%$^*";
    string name3 = "李明燮";

    function getLength2 () view public returns(uint) {
        return bytes(name2).length; // 6 有些特殊字符是占1字节
    }

    function getLength3 () view public returns(uint) {
        return bytes(name3).length; // 9 一个中文占了3字节UTF8类型
    }

    function getChinesName3() view public returns(bytes) {
        return bytes(name3); // 0xe69d8ee6988ee787ae
    }
}
```

## 2.bytes

### 2.1 固定长度bytes之间的转换

```sol
pragma solidity ^0.4.0;

contract changeBytes{
    bytes9 name = 0xe69d8ee6988ee787ae;

    function getBytes1() view public returns(bytes1){
        return bytes1(name); //0xe6
    }

    function getBytes2() view public returns(bytes2){
        return bytes2(name); //0xe69d
    }

    function getBytes3() view public returns(bytes16){
        return bytes16(name); // 0xe69d8ee6988ee787ae00000000000000
    }
}
```

### 2.2 固定长度bytes转动态bytes

```sol
pragma solidity ^0.4.0;

contract fixToDynamic{
    bytes9 name = 0x6c6c6d696e67786965;

    function fixToDynamicBytes() view public returns(bytes) {
        // return bytes(name); 编译报错
        bytes memory newName = new bytes(name.length);
        for(uint i = 0; i < name.length; i++){
            newName[i] = name[i];
        }
        return newName; // 0x6c6c6d696e67786965
    }
}
```

### 2.3 bytes转string

```sol
pragma solidity ^0.4.0;

contract BytesToString {
    bytes name = new bytes(2);

    function Init() public {
        name[0] = 0x7a;
        name[1] = 0x68;
    }

    function bytesToString() view public returns(string){
        return string(name); // zh
    }
}
```

### 2.4 固定长度bytes转string

```sol
pragma solidity ^0.4.4;

contract Bytes32ToString {
    bytes2 name = 0x7a68;

    // function changeIt() view public returns(string){
        // return string(name); //报错 不能这样转换
    // }

    // 参数  0x7a68000000000000000000000000000000000000000000000000000000000000
    function bytes32ToString(bytes32 _newName) pure public returns(string){
        bytes memory newName = new bytes(_newName.length);
        for(uint i=0; i<_newName.length; i++){
            newName[i] = _newName[i];
        }
        return string(newName); // zh
    } 

    // 参数  0x7a68000000000000000000000000000000000000000000000000000000000000
    function bytes32ToString2(bytes32 _newName) pure public returns(string){
        uint count = 0;
        for(uint i=0; i<_newName.length; i++){
            bytes1 char = _newName[i];
            if (char != 0 ){
                count++;
            }
        }

        bytes memory newName = new bytes(count);
        for(uint j=0; j<count; j++){
            newName[j] = _newName[j];
        }
        return string(newName); // zh
    } 
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
