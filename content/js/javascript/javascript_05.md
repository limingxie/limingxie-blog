---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_函数(5)"
date: 2022-05-06T23:05:49+08:00
tags: [
    "js",
    "javascript",
    "function",
]
categories: [
    "JavaScript",
]
---

函数也是对象，封装了一些功能(代码)，可以调用。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript">
        console.log("--------------------function 调用方式--------------------");

        //写new就代表调用构造函数constructor
        var fun = new Function();
        console.log("fun type:", typeof fun);   //fun type: function

        fun = new Function("console.log('hello world')");
        console.log(fun);   /* ƒ anonymous(
                                ) {
                                console.log('hello world')
                                } */
        fun();  //hello world

        //fun(); 执行函数
        //fun: 函数对象

        //因为函数也是对象，所以可以给一个属性值
        fun.age = 18;
        console.log(fun.age);   //18

        // 声明匿名函数
        var fun1 = function () {
            console.log('hello world');
        }

        //执行匿名函数
        fun1();  //hello world

        function fun2() {
            console.log('111111');
            console.log('222222');
        }

        fun2();  //111111 222222

        function fun3(a, b) {
            console.log(a + b);
        }

        fun3(1, 2);  //3
        fun3();     //NaN  
        fun3(1);    //NaN
        fun3(123, "Hello");  //123Hello
        fun3(1, 2, 3, "Hello");  //3

        //给函数加 return
        function fun4(a, b) {
            var c = a + b;
            return c;
        }

        //需要用便来接返回值
        var result = fun4(1, 2);
        console.log("result:", result); //result: 3

        var obj = { age: 18, name: "小明" };

        //给函数传对象参数
        function fun5(a) {
            console.log("年龄:", a.age, "姓名:", a.name);
        }

        fun5(obj);  //年龄: 18 姓名: 小明

        //给函数传函数参数
        function fun6(a) {
            a(obj);
        }

        fun6(fun5); //年龄: 18 姓名: 小明

        //函数的返回对象
        function fun7() {
            return { name: "小明", age: 18 };
        }

        var obj1 = fun7();
        console.log(obj1.name); // 小明

        //函数内的函数
        function fun8() {
            function fun9() {
                console.log("func9");
            }
            return fun9;
        }

        var a = fun8();
        a(); //func9
        fun8()(); //func9

        //匿名函数的另一种用法
        (function() {
            console.log("我是匿名函数");
        })(); // 我是匿名函数

        //匿名函数的另一种用法
        (function(a, b) {
            console.log("a:", a);
            console.log("b:", b);
        })(12, 13);     //a: 12 b: 13

        var obj = {name:"张三",age:18};
        obj.sayName = function() {
            console.log("我的名字是:", this.name);
        }
        //掉用对象方法(method)   // fun() 前面没有指定对象的叫函数
        obj.sayName(); //我的名字是: 张三
        //console.log() document.write() 都是对象方法

        //可以用这种方式查看属性
        for(var n in obj){
            console.log(n);
        }  // name age sayName

        //可以用这种方式查看属性值
        for(var n in obj){
            console.log(obj[n]);
        }  // 张三 18 ƒ () { console.log("我的名字是:", this.name);}

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
