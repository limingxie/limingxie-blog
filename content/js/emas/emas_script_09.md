---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_对象和数值扩展方法(9)"
date: 2022-05-17T09:05:49+08:00
tags: [
    "es6",
    "number",
    "object",
]
categories: [
    "Emas",
]
---

这里整理了Object和Number扩展方法。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("-------------- number 扩展 --------------");
        {
            // Number.EPSILON 是 JS最小精度
            console.log(Number.EPSILON); //2.220446049250313e-16
            console.log(0.1 + 0.2); //0.30000000000000004
            function equal(a, b) {
                return Math.abs(a - b) < Number.EPSILON;
            }
            console.log(equal(0.1 + 0.2, 0.3)); //true

            //1. 二进制和八进制
            //0b：二进制
            //0o：八进制
            //0x：十六进制
            console.log(0b111110111); // 503

            //2. Number.isFinite 检查一个值是否为有限的
            console.log(Number.isFinite(15)); //true
            console.log(Number.isFinite(Infinity)); //false

            //3. Number.isNaN   检查一个值是否为NaN
            console.log(Number.isNaN(123)); //false
            console.log(Number.isNaN(NaN)); //true

            //4. Number.paresInt    
            console.log(Number.parseInt('12.34')); //12
            console.log(Number.parseFloat('12.34aa')); //12.34

            //5. Number.isInteger
            console.log(Number.isInteger(25)); //true
            console.log(Number.isInteger(2.5)); //false

            //6. Math.trunc 将数字的小数部分去掉
            console.log(Math.trunc(3.5)); //3

            //7. Math.sign 判断一个数 正数，负数 或 零
            console.log(Math.sign(-5)); //-1
            console.log(Math.sign(0)); //0
            console.log(Math.sign(5)); //1
        }

        console.log("-------------- object方法 扩展 --------------");

        {
            //1. Object.is(value1, value2)  判断两个值是否严格相等
            console.log(Object.is(10, 10)); //true
            console.log(Object.is(+0, -0)); //false
            console.log(Object.is(NaN, NaN)); //true
            console.log(NaN === NaN); //false

            //2. Object.assign(target, ...sources)  合并多个对象
            const config1 = {
                host: 'localhost',
                port: 8080,
                name: 'test',
                password: '123456',
                test1: 'aaa',
            }

            const config2 = {
                host: '192.168.1.1',
                port: 3306,
                name: 'root',
                password: '1111',
                test2: 'bb'
            }

            //后面的覆盖前面的
            let config = Object.assign(config1,config2);
            console.log(config);//{host: '192.168.1.1', port: 3306, name: 'root', password: '1111', test1: 'aaa', test2: 'bb'}

            //3. Object.setPrototypeOf Object.getPrototypeof
            const school ={
                name:'小学'
            }
            const cities = {
                area: ['海淀', '朝阳', '顺义']
            }

            Object.setPrototypeOf(school, cities);
            console.log(school); // {area: Array(3), name: "小学"}
            console.log(Object.getPrototypeOf(school)); // {area: Array(3)}
        }

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
