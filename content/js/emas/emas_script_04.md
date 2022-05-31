---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_Symbol(4)"
date: 2022-05-12T09:05:49+08:00
tags: [
    "es6",
    "Symbol",
]
categories: [
    "Emas",
]
---

这里整理了ES6新增的类型Symbol。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------Symbol--------------");
        /*
        ES引入了新的数据类型Symbol。标识独一无二的值。
        JavaScript第七种数据类型。是一种类似于字符串的数据类型。
        特点：
        1.Symbol的值是唯一的，用来解决命名冲突的问题。
        2.Symbol值不能与其他数据进行运算
        3.Symbol定义的对象属性不能使用 for...in 循环遍历, 
          但是可以用Reflect.ownKeys方法获取对象的所有键名
        */
        let s1 = Symbol();
        console.log(s1, typeof s1); //Symbol() "symbol"

        let s2 = Symbol('小明');
        let s3 = Symbol('小明');
        console.log(s2 === s3); //false

        let s4 = Symbol.for("小红");
        console.log(s4, typeof s4); // Symbol(小红) 'symbol'

        let s5 = Symbol.for("小红");
        console.log(s4 === s5); //true
        console.log(Symbol.keyFor(s5)); //小红

        // 查看描述
        let s6 = Symbol.for("小刚"); //小刚
        console.log(s6.description);

        // 转换string
        let s7 = Symbol('小张');
        console.log(String(s7)); //Symbol(小张)

        // bool判断
        let s8 = Symbol();
        console.log(Boolean(s8)); //true

        //USONB 
        // u undefined
        // s string symbol
        // o object
        // n number
        // b boolean

        let kind = Symbol("kind");

        let car = {
            name: "奔驰",
            // [Synmbol('kind')]: '赛车游戏',
            [kind]: '小轿车',
            [Symbol("col")]: '红色',
            [Symbol.for("colour")]: '黑色',
            [Symbol.for('加速')]: function () {
                console.log("需要加速");
            },
            [Symbol('减速')]: function () {
                console.log("需要减速");
            }
        };

        console.log(car[kind]); //小轿车
        console.log(car.kind); //undefined
        console.log(car[Symbol("col")]); //undefined
        console.log(car[Symbol.for("colour")]); //黑色

        car[Symbol.for("加速")](); //需要加速
        //car[Symbol('减速')](); // TypeError: car[Symbol(...)] is not a function

        //Symbol.for() 都会重复使用同一个Symbol值
        //Symbol() 他是每次都会用不同的Symbol值

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
