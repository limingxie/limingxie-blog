---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_运算符(2)"
date: 2022-05-05T23:05:49+08:00
tags: [
    "js",
    "javascript",
]
categories: [
    "JavaScript",
]
---

这里整理了常用的运算符，在代码的注释里一起描述了运算符的含义。

## 1.加法(+)

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------加法--------------------");
        var aa = 12 + 1;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 13

        aa = 12 + true;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 13

        aa = true + false;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 1

        aa = 12 + null;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 12

        aa = 12 + NaN;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: NaN

        aa = 12 + undefined;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: NaN

        aa = "你好" + "世界";                 //加法中只要有字符串，会先转字符串后在做加法
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: 你好世界

        aa = 1 + 2 + "3";
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: 33

        aa = 1 + "2" + 3;
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: 123

        aa = true + "你好";
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: true你好

        aa = 12 + "";
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: 12

        aa = null + "";
        console.log("aa type:", typeof aa); //aa type: string
        console.log("aa:", aa);             //aa: null

        aa = "12";
        aa = +aa;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 12

        aa = 1 + +"2" + 3;

        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: 6

    </script>
</head>

<body>
</body>

</html>
```

## 2.减法(-)

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------减法--------------------");

        var bb = 12 - 1;
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: 11

        bb = 12 - "1";
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: 11

        bb = 12 - true;
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: 11

        bb = 12 - NaN;
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: NaN

        bb = 12 - null;
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: 12

        bb = 12 - undefined;
        console.log("bb type:", typeof bb); //bb type: number
        console.log("bb:", bb);             //bb: NaN

        aa = "12";
        aa = -aa;
        console.log("aa type:", typeof aa); //aa type: number
        console.log("aa:", aa);             //aa: -12

    </script>
</head>

<body>
</body>

</html>
```

## 3.乘法(*),除法(/),取模(%)

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------乘法(*),除法(/),取模(%)--------------------");

        var cc = 12 * 2;
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: 24

        cc = 12 * "2";
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: 24

        cc = 12 * null;
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: 0

        cc = 12 * NaN;
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: NaN

        cc = 12 * undefined;
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: NaN

        cc = 12 / "2";
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: 6

        cc = 12 % "5";
        console.log("cc type:", typeof cc); //cc type: number
        console.log("cc:", cc);             //cc: 2

    </script>
</head>

<body>
</body>

</html>
```

## 4. ++ 和 --

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
       console.log("--------------------【 ++ 和 -- 】--------------------");

        var cc = 1;
        console.log(cc++);      // 1
        console.log("cc:", cc); //cc: 2

        cc = 1;
        console.log(++cc);      // 2
        console.log("cc:", cc); //cc: 2

        cc = 2;
        console.log(--cc);      // 1
        console.log("cc:", cc); //cc: 2

        cc = 1;
        //  1  + 3 + 3
        cc= cc++ + ++cc + cc;
        console.log("cc:", cc); //cc: 7

        console.log("--------------------【 += -= *= /= %= 】--------------------");
        //和其他语言一样，这里就不做整理了。

    </script>
</head>

<body>
</body>

</html>
```

## 5. !  &&  ||

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
       console.log("--------------------【 !  &&  || 】--------------------");
        // ! 取反    && 与   || 或  
        var dd = "true";

        dd = !!dd;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: true

        dd = !dd;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: false

        //如果两个都是true,返回后面的
        dd = 5 && 6;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: 6

        //如果有一个是false,返回前面的值
        dd = 0 && 6;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: 0

        dd = NaN && 0;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: NaN

        //返回靠前的true, 都是false返回后面的false
        dd = 2 || 1;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: 2

        dd = 0 || 2;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: 2

        dd = NaN || 0;
        console.log("dd type:", typeof dd); //dd type: boolean
        console.log("dd:", dd);             // dd: 0

    </script>
</head>

<body>
</body>

</html>
```

## 6. 比较预算符(> >= < <=  != == !== ===)

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------【 > >= < <= 】--------------------");
        
        console.log(5 > 6); //false
        console.log(5 <= 6); //true
        console.log(1 >= true); //true
        console.log(10 >= null); //true
        
        //任何值和NaN比较都是false
        console.log(10 >= "Hi"); //false
        console.log(10 <= "Hi"); //false

        console.log("7" > "5"); //true
        //字符串会从左开始比较第一个字符后，大小直接返回，诺一样比较下一个值。
        console.log("11" > "5"); //false
        //中文比较没什么意义
        console.log("你" > "我"); //false
        
        console.log("--------------------【 != == !== === 】--------------------");

        console.log(10 != "10"); //false
        console.log(10 == "10"); //true
        console.log(true == "1"); //true
        console.log(null == undefined); //true

        //NaN 不跟任何值相等包括本身
        var ee = NaN;
        console.log(ee == NaN); //false
        //是否是NaN, 需要用 isNaN() 函数判断
        console.log(isNaN(ee)); //true

        // !== 和 === 不会自动转换类型，所以类型和值都会做比较, 类型不一样直接返回false
        console.log(10 === "10"); //false
        console.log(null == undefined); //false
    </script>
</head>

<body>
</body>

</html>
```

## 7. bool ? value1 : value2

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------【 bool ? value1 : value2】--------------------");
        
        var ff = 1==1 ? "true" : "false";
        console.log(ff); //true

        //取最大值
        var a = 10, b = 40 ,c =30;

        //不建议这么写
        var max = a > b ? a > c ? a : c : b > c ? b : c;
        console.log(max); //40

        // 推荐方式
        var max = a > b ? a : b;
        max = max > c ? max : c;
        console.log(max); //40

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
