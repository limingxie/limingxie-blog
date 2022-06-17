---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_函数和参数(3)"
date: 2022-05-11T09:05:49+08:00
tags: [
    "es6",
    "function",
    "parameter",
]
categories: [
    "Emas",
]
---

这里整理了ES6新增的函数(箭头函数)和参数(默认值和rest)相关内容。<!--more-->

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------1.箭头函数--------------");
        let func1 = function (a, b) {
            console.log("a + b = ", a + b);
        };

        // 1.声明
        let func2 = (a, b) => {
            console.log("a + b = ", a + b);
        };

        func1(1, 2); // a + b =  3
        func2(1, 2); // a + b =  3

        // 2.this是静态的，this始终指向函数声明时坐在的作用域，不会变

        function getName1(){
            console.log(this.name);
        }

        let getName2 = () => {
            console.log(this.name);
        }

        window.name = '小张';
        const xiaoming = {
            name: '小明'
        }
        getName1(); //小张
        getName2(); //小张

        getName1.call(xiaoming); //小明
        getName2.call(xiaoming); //小张

        // 3.不能作为构造函数实例化对象
        let Person = (name, age) =>{
            this.name = name;
            this.age = age;
        }

        // let xiaozhang = new Person('小张', 20); //TypeError: Person is not a constructor
        // console.log(xiaozhang); 

        // 4.不能使用arguments变量会报错
        // let fn = (a,b) => {
        //     console.log(arguments);
        // }
        // fn(1,2); // ReferenceError: arguments is not defined

        // 5.箭头函数的简写
        // 1)省略小括号，当形参只有一个时，可以省略
        let add = n => {
            return n + n;
        }
        console.log(add(5)); //10

        // 2)省略大括号，当函数体只有一条语句时，可以省略
        // 3)省略return，当函数体只有一条语句且是return语句时，可以省略
        let add2 = n => n + n;
        console.log(add(4)); //8

        // 练习
        const arr = [1,4,5,62,6,8,3,41,72,93,10];
        let result = arr.filter(function(item){
            if(item % 2 === 0){
                return true;
            } else {
                return false;
            }
        });
        console.log(result); //(6) [4, 62, 6, 8, 72, 10]

        result1 = arr.filter(item => item % 2 === 0); //(6) [4, 62, 6, 8, 72, 10]

        //箭头函数适合于，和this无关的回调，定时器，数组方法回调
        //不适合this有关的，比如DOM事件回调，对象的回调。

        console.log("--------------2.参数--------------");

        // 1.参数默认值: 给默认值的参数一般放后面
        function func3(a, b = 10) {
            console.log("a + b = ", a + b);
        }
        
        func3(2); // a + b =  12

        // 2.参数默认值: 参数可以是一个对象，也可以是一个函数
        function printUser({name, age = 10, address = '北京'}) {
            console.log(name, age, address);
        }

        let user = {
            name: '小明',
            address : '上海'
        }

        printUser(user); //小明 10 上海

        // 3.rest 参数：用于获取函数的实参，用来代替arguments

        //ES5的方式
        function printArg() {
            console.log(arguments);
        }

        printArg('北京', '上海', '深圳'); //(3) ["北京", "上海", "深圳", callee: ƒ, Symbol(Symbol.iterator): ƒ]

        //ES6的 rest方式
        function printArg1(...args){
            console.log(args);
        }

        printArg1('北京', '上海', '深圳'); //(3) ["北京", "上海", "深圳"]

        //rest 参数必须放最后，否则报错 //TypeError: Rest parameter must be last formal parameter
        function printArg2(a,b,...args){
            console.log(a);
            console.log(b);
            console.log(args);
        }

        printArg2(1,2,3,4); // 1 2 [3,4]

        // 4.扩展运算符
        const aa = ['小明', '小红', '小刚'];

        function fn1() {
            console.log(arguments);
        }

        fn1(aa); // [Array(3), callee: ƒ, Symbol(Symbol.iterator): ƒ]

        //下面是等同效果
        fn1(...aa); // (3) [ '小明', '小红', '小刚' ]
        fn1('小明', '小红', '小刚'); // (3) [ '小明', '小红', '小刚' ]

        // 练习
        //1.合并
        const bb = ['小明', '小红'];
        const cc = ['小刚', '小美'];
        const bbcc = [...bb, ...cc];
        console.log(bbcc); // [ '小明', '小红', '小刚', '小美' ]

        //2.克隆
        const ee = [1,2,3];
        const ff = [...ee]; // [1,2,3]


    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
