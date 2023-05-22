---
author: "li_mingxie"
title: "【solidity】getter方法(11)"
date: 2023-05-11T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "eth",
    "solidity",
]
---

这篇文章简单的整理了`继承和调用权限`相关的内容。  <!--more-->  

## 1.默认生成方法

```sol
pragma solidity ^0.4.0;

contract getter {
    // public 默认生成一个get方法给我们外部调用
    uint public num = 100;

    // 等价于如下方法
    // function num() external view returns(uint){return num;}

    // function test() {
        // num(); 编译报错
        // this.num(); 正常执行，和上一行代码判断出是 external 类型
    // }

    mapping(uint => string) public map;

    // mapping 的 public 默认生成如下方法
    // function map(uint key) external returns(string) {
    //     return map[key];
    // }

    function test2() public {
        map[2]="aa";
    }

    function test3()view public returns(string){
        return this.map(2);
    }
}
```

## 2.复杂的getter

```sol
pragma solidity ^0.4.0;

contract getter {
    mapping(uint => mapping(uint => mapping(uint => string))) public map;

    function test() {
        map[0][1][2] = "aa";
    }
}
```

```
0,1,2
0:string: aa
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
