---
author: "li_mingxie"
title: "【solidity】结构体struct(15)"
date: 2023-05-15T23:28:49+08:00
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

这篇文章简单的整理了`结构体struct`相关的内容。  <!--more-->  

## 1.memory和storage简单例子

```sol
pragma solidity ^0.4.0;

contract structTest {
    
    struct student {
        uint grade;
        string name;

        // student stu;  不能包含自己
        // student[] stulist; // 这是可以的，因为这是指针
        // mapping(uint=>student) map; //这也可以
    }

    function init1() pure public returns(uint, string) {
        // 函数体内struct默认是storage类型，需声明memory
        student memory s = student(100, "zhangsan");
        return (s.grade, s.name);
    }

    function init2() pure public returns(uint, string) {
        student memory s = student({ grade: 100, name: "zhangsan"});
        return (s.grade, s.name);
    }
}
```

## 2.mapping

```sol
pragma solidity ^0.4.0;

contract structTest {
    
    struct student {
        uint grade;
        string name;

        mapping(uint=>string) map;   
    }

    student meimei;

    function init() public returns(uint, string, string) {
        // 函数体内struct默认是storage类型，需声明memory
        // 初始化结构体的时候，可以不用做mapping类型的初始化
        student memory s = student(100, "zhangsan");

        // s.map[0] = "hello world"; 报错 memory 类型不能操作mapping
        meimei = s;
        meimei.map[0] = "hello world"; // 只能是storage类型才能操作mapping类型
        return (s.grade, s.name, meimei.map[0]);
    }
}
```

## 3.struct类型的参数

```sol
pragma solidity ^0.4.0;

contract structTest {
    
    struct student {
        uint grade;
        string name;
    }

    // 合约内部的struct为参数的方法，只能是用internal
    // 参数默认是 memory 类型
    function test(student s) internal {

    }
}
```

## 4.memory和storage

storage => storage

```sol
pragma solidity ^0.4.0;

contract structTest {
    
    struct student {
        uint grade;
        string name;
    }

    student stu;

    function test(student storage s) internal {
        student storage meimei = s;
        meimei.name = "zhangsan";
    }

    function call() public returns(string){
        test(stu);
        return stu.name; // zhangsan
    }
}
```

memory => memory

```sol
contract structTest {
    
    struct student {
        uint grade;
        string name;
    }


    function test(student memory s) internal {
        student memory meimei =s;
        meimei.name = "zhangsan";
    }

    function call() public returns(string){
        student memory stu = student(100, "stu");
        test(stu);
        return stu.name; // zhangsan
    }
}
```

## 5.memory和storage互转

```sol
pragma solidity ^0.4.0;

contract structTest {
    
    struct student {
        uint grade;
        string name;
    }

    student stu;

    function test(student memory s) internal {

        // 这时候只是把值赋值过来了，没有引用。
        stu = s;
        s.name = "zhangsan";
    }

    function call() public returns(string){
        student memory tmp = student(100,"tmp");
        test(tmp);
        return stu.name; // tmp
    }
}
```

```sol
pragma solidity ^0.4.0;

contract structTest6 {
    
    struct student {
        uint grade;
        string name;
    }

    student stu = student(100, "stu");

    function test(student storage s) internal {
        // 这里只是把值赋给meimei，后续对meimei操作和stu没关
        student memory meimei =s;
        meimei.name = "zhangsan";
    }

    function call() public returns(string){
        test(stu);
        return stu.name; // stu
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
