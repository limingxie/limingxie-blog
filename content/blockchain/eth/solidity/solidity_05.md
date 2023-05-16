---
author: "li_mingxie"
title: "【solidity】数组(05)"
date: 2023-05-05T23:28:49+08:00
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

这篇文章简单的整理了`数组`相关的内容。  <!--more-->  

## 1.固定长度数组

```sol
pragma solidity ^0.4.0;

contract FixArray{
    uint[5] arr = [1,2,3,4]; 

    function Init() public{
        arr[0] = 100;
        arr[1] = 200;
    }
    function getArrayContent() view public returns(uint[5]){
        return arr; // 100,200,3,4,0
    }

    function getGrade() view public returns(uint){
        uint grade = 0;
        for(uint i=0; i<5; i++){
            grade = grade + arr[i];
        }

        return grade; // 307=100+200+3+4+0
    } 

    function getLength() view public returns(uint){
        return arr.length; // 5
    }

    function push(uint){
        
    }
}
```

## 2.可变长度数组

```sol
pragma solidity ^0.4.0;

contract DynamicArray {
    uint[] grade= [1,2,3,4,5];

    function getContent()view public returns(uint[]){
        return grade;
    }

    function getLength()view public returns(uint){
        return grade.length;
    }

    function changeContent() {
        grade[0] = 100;
        grade[1] = 200;
    }

    function add()view public returns(uint){
        uint sum = 0 ;
        for(uint i=0; i<grade.length; i++){
            sum += ;
        }
        return grade;
    }

    function changeLength() {
        grade.length = 1;
    }

    function changeLength2() {
        grade.length = 10;
    }
}
```

## 3.二维数组

```sol
pragma solidity ^0.4.0;

contract TwoArry{
    uint[2][3] grade = [[1,2],[3,4],[5,6]];

    function getLength() view public returns(uint) {
        return grade.length; // 3
    }

    function getTowLength() view public returns(uint){
        return grade[0].length; // 2
    }

    function getContent() view public returns(uint[2][3]){
        return grade; // 1,2,3,4,5,6
    }

    function add() view public returns(uint){
        uint sum = 0;
        for(uint i=0; i<grade.length; i++){
            for(uint j=0; j<grade[i].length; j++){
                sum +=grade[i][j];
            }
        }
        return sum; // 21
    }

    function changeContent() public {
        grade[1][0] = 100; //1,2,100,4,5,6
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
