---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_call_apply_arguments(9)"
date: 2022-05-08T08:55:49+08:00
tags: [
    "js",
    "call",
    "apply",
    "arguments",
    "javascript",
]
categories: [
    "JavaScript",
]
---

这里整理了函数的call, apply, arguments。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript">
        console.log("--------------------call apply--------------------");

        function fun() {
            console.log(this);
        }

        var obj = { name: "张三", age: 18 };
        var obj2 = { name: "李四", age: 20 };

        //call和apply可以把第一个参数作为this
        fun();//Window {...}
        fun.call(obj); //{name: '张三', age: 18}
        fun.apply(obj); //{name: '张三', age: 18}

        function sayName(){
            console.log("我叫:" + this.name);
        }

        var obj = { name: "张三", age: 18 , sayName: sayName};
        var obj2 = { name: "李四", age: 20 };

        //方法里的this是传的object
        obj.sayName.apply(obj2); //我叫:李四

        //call和apply的区别
        function fun(a,b){
            console.log("this:",this);
            console.log("a:",a);
            console.log("b:",b);
        }

        //call是一个一个传
        fun.call(obj,1,2); //this: {name: '张三', age: 18}   a: 1   b: 2
        //apply是封装到数组里传
        fun.apply(obj,[1,2]); //this: {name: '张三', age: 18}   a: 1   b: 2

        console.log("--------------------arguments--------------------");

        //arguments是一个类数组对象，可以用来访问函数的参数, 他不是数组，是类数组
        //函数的实参会被放到arguments里
        function fun() {
            //所有实参的长度
            console.log(arguments.length);
            //所有实参
            console.log(arguments);
            //指定一个实参
            console.log(arguments[0]);
            //当前执行的函数的对象
            console.log(arguments.callee);
        }

        fun(1, 2, 3, 4, 5); //5
                            // arguments: (1, 2, 3, 4, 5)
                            //1
                            /* 当前执行的函数的对象
                                ƒ fun() {
                                    console.log(arguments.length);
                                    console.log(arguments);
                                    console.log(arguments[0]);
                                    console.log(arguments.callee);
                                }
                            */
        

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
