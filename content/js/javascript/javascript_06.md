---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_作用域(5)"
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

这里整理了变量的作用域，全局和函数内。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript">
        console.log("--------------------变量的作用域--------------------");

        //函数里的变量只适用于函数内部，不会影响全局变量。
        function fun() {
            var a = 10;
        }
        fun();
        //console.log(a); //a is not defined

        //全局变量都是属于window这个类型的属性
        //var a = 10 和 window.a = 10; 是一样的效果

        b = 10; //不写var 声明的变量也等同于 window.b = 10;
        fun();          //两个效果一样；
        window.fun();   //两个效果一样；

        window.aa = 3;
        console.log("aa:", aa); // aa: 3   window.aa = 3;  等同于 aa = 3;

        //不报错的原因是，var声明的变量都在最前头先声明，赋值是跟着程序走。
        console.log("dd:", dd); //dd: undefined  
        var dd = 123;

        fun2(); //fun2
        //这么声明的函数也是和var一样，代码开始之前会被编译。
        function fun2(){
            console.log("fun2");
        }

        //fun3(); //fun3 is not a function, 因为只是声明var  所以等同于 undefinded();
        //但是new出来的函数是不会先编译的
        var fun3 = function(){
            console.log("fun3");
        }

        //函数内的变量是函数外调用不了，function 结束 
        var cc = 10;
        
        function fun4(){
            var ee = 20;
            console.log("cc:", cc); //cc: 10
        }

        fun4(); //cc: 10
        //console.log(ee); // ee is not defined

        //相同的变量名，先用函数内部的再用外部的
        var ff = 10;

        function fun5(){
            var ff = "函数ff";
            console.log("ff:", ff); //ff: 函数ff
        }

        console.log("ff:", ff); //ff: 10

        //函数里想用全局变量 使用 window.变量名
        var gg = 10;

        function fun5(){
            var gg = "函数gg";
            console.log("gg:", window.gg); //gg: 函数gg
        }

        function fun6(){
            console.log("gg:", gg); // gg: undefined   和全局处理很像
            var gg = "函数gg";
        }
        fun6();

        
        var hh = 10;
        function fun7(){
            console.log("hh:", hh); //hh: 10
            var hh = 5;
        }
        fun7();     //hh: undefined


        var hh = 10;
        function fun7(){
            console.log("hh:", hh);
            hh = 5; //全局变量
        }
        fun7();     //hh: 10

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
