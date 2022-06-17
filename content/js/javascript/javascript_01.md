---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_结构和类型(1)"
date: 2022-04-29T09:05:49+08:00
tags: [
    "js",
    "javascript",
]
categories: [
    "JavaScript",
]
---

本想学Nodejs的，结果发现自己的JavaScript的基础太差,  
(几年前看了一下基本语法后，就没有碰过了。)
而且很多资料大部分都是对JavaScript有所了解为前提。  <!--more-->

只能先安心复习一下JavaScript的基础，然后再去学习Nodejs了...^^;;

## 1.JavaScript

还是按照惯例，先看看JavaScript在[维基百科](https://zh.wikipedia.org/wiki/JavaScript)里是怎么解释的。  

> JavaScript（通常缩写为JS）是一种高级的、解释型的编程语言。  

```
JavaScript是一门基于原型、头等函数的语言，是一门多范式的语言，它支持面向对象程序设计，指令式编程，以及函数式编程。  
它提供语法来操控文本、数组、日期以及正则表达式等，不支持I/O，比如网络、存储和图形等，但这些都可以由它的宿主环境提供支持。  
它已经由ECMA（欧洲电脑制造商协会）通过ECMAScript实现语言的标准化。  
它被世界上的绝大多数网站所使用，也被世界主流浏览器（Chrome、IE、Firefox、Safari、Opera）支持。
```

JavaScript在传统意义上被实现为一种解释语言，但在最近[何时？]，它已经可以被即时编译（JIT）执行。  
随着最新的HTML5和CSS3语言标准的推行它还可用于游戏、桌面和移动应用程序的开发和在服务器端网络环境运行 如Node.js。  

#### JavaScript构成

* **ECMAScript:** 描述了该语言的语法和基本对象
* **文档对象模型(DOM):** 描述处理网页内容的方法和接口
* **浏览器对象模型(BOM):** 描述与浏览器进行交互的方法和接口

## 2.JavaScript代码结构

javascript一般在html里的header里写，如下：

```html
<!DOCType html>
<html>

<head>
    <script type="text/javascript">
        console.log("---------------javascript 语法测试-----------------");
    </script>
</head>

<body>
    <!-- 可以这么加javascript,但是为了可读性不建议这么加 -->
    <button onclick="alert('点我了吗？');">点我</button>
    <a href="javascript:alert('怎么我也点了？');">点我一下</a>
</body>

</html>
```

也可以引用外部js文件：

outjs.js =>

```html
document.write("我是外部js文件!");
```

jshtml.html =>

```html
    <script type="text/javascript" src="outjs.js">
        console.log("----------------外部js文件测试----------------");
        console.log("看看界面上有没有外部文件的提示");
    </script>
```

## 3.JavaScript的基本数据类型

JavaScripty有6种数据类型。String，Number，Boolean，Null，Undefined，Object。  
具体的方式直接用代码看看：  

#### 3.1 String(字符串)

```html
    <script type="text/javascript">
        console.log("------------------String----------------------");

        var str = "hello " + "world!";
        console.log("str:", str);   //str: hello world!

        str = 123
        console.log("str:", str);       //str: 123

        str = "特殊符号\",\\, \'"; // \n \t \\ \', \"
        console.log("str:", str);       //str: 特殊符号",\, '

        var c = "123";
        console.log("c.toString():", c.toString());   //c.toString(): 123
        console.log("String(c):", String(c));   //String(c): 123

        // console.log("null.toString():", null.toString());   报错
        console.log("NaN.toString():", NaN.toString());   // NaN.toString(): NaN
        // console.log("undefined.toString():", a.toString());   报错

        console.log("String(null):", String(null));  // String(null): null
        console.log("String(NaN):", String(NaN));   // NaN.toString(): NaN
        console.log("String(undefined):", String(undefined));   // String(undefined): undefined
    </script>
```

#### 3.2 Number(数值)

```html
    <script type="text/javascript">
        console.log("------------------Number----------------------");

        var num = 12;
        console.log("num type:", typeof num);    //num type: number
        console.log("num:", num);       //num: 12

        var strNum = "12";
        console.log("strNum type:", typeof strNum);  //strNum type: string
        console.log("strNum:", strNum);     //strNum: 12

        // Number.MAX_VALUE => 1.7976931348623157e+308
        // Number.MIN_VALUE => 5e-324
        // Number.MAX_VALUE * 10 => Infinity 正无穷大  -Infinity 负无穷大
        console.log("Infinity:", Number.MAX_VALUE * 10);    //Infinity: Infinity
        console.log("Infinity type:", typeof Infinity);     //Infinity type: number

        //Nan => Not A Number 也是 number类型
        console.log("NaN:", "abc" * "def");         //NaN: NaN
        console.log("NaN type:", typeof NaN);       //NaN type: number

        //小数点计算问题
        console.log("0.1 + 0.2 = ", 0.1 + 0.2);     //0.1 + 0.2 =  0.30000000000000004

        console.log("Number(Infinity):", Number(Infinity));         //Number(Infinity): Infinity
        console.log("Number(NaN):", Number(NaN));                   //Number(NaN): NaN
        console.log("Number(null):", Number(null));                 //Number(null): 0
        console.log("Number(undefined):", Number(undefined));       //Number(undefined): NaN

        console.log("parseInt('123px'):", parseInt('123px'));       //parseInt('123px'): 123
        console.log("parseInt('123.56'):", parseInt('123.56'));     //parseInt('123.56'): 123
        console.log("parseInt(123.56):", parseInt(123.56));         //parseInt(123.56): 123
        console.log("parseInt(null):", parseInt(null));             //parseInt(null): NaN
        console.log("parseInt(NaN):", parseInt(NaN));               //parseInt(NaN): NaN
        console.log("parseInt(undefined):", parseInt(undefined));   //parseInt(undefined): NaN
        console.log("parseInt(123.56px):", parseFloat('123.56px'));       //parseInt(123.56px): 123.56
        console.log("parseInt(123.56.78):", parseFloat('123.56.78'));      //parseInt(123.56.78): 123.56

        console.log("parseInt('123px',10):", parseInt('123px', 10));       //parseInt('123px'): 123 后面的参数标识进制(这里说的是10进制)
        // 0x 16进制
        // 0 8进制
        // 0b 2进制
    </script>
```

#### 3.3 Boolean(布尔值)

```html
    <script type="text/javascript">
        console.log("------------------Boolean----------------------");

        var bool = true;
        console.log("bool type:", typeof bool);     //bool type: boolean
        console.log("bool:", bool);         //bool: true

        /*
            数字除了0和NaN是false, 其他都是true;
            字符串除了空字符串都是true;
            null和undefined都是false;
            Object对象转换为true;
        */

        console.log("bool:", Boolean(1));         //bool: true
        console.log("bool:", Boolean(0));         //bool: true
        console.log("bool:", Boolean("true"));         //bool: true
    </script>
```

#### 3.4 Null(空值)

```html
    <script type="text/javascript">
        console.log("------------------Null----------------------");

        console.log("null:", null);         //null: null
        console.log("null type:", typeof null);     //null type: object
    </script>
```

#### 3.5 Undefined(未定义)

```html
    <script type="text/javascript">
        console.log("------------------Undefined----------------------");

        console.log("undefined:", undefined);         //undefined: undefined
        console.log("undefined type:", typeof undefined);     //undefined type: undefined
    </script>
```

#### 3.6 Object(对象:引用类型)

可以参考后续文章  
[【JavaScript笔记】JavaScript基础_Object对象(4)）](https://limingxie.github.io/js/javascript/javascript_04/)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
