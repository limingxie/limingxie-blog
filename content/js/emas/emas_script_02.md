---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_变量(2)"
date: 2022-05-11T09:05:49+08:00
tags: [
    "es6",
    "let",
    "const",
]
categories: [
    "Emas",
]
---

这里整理了ES6新增的变量声明方法。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------1.let 用法--------------")
        //1.声明
        let a;
        let b, c, d;
        let e = 10, f = 'haha', g = [];

        // 2.同名的变量报错
        let h;
        //let h; 同名的变量报错

        //3. 作用域是代码块 {}
        // if else for while
        {
            var s = '小明';
            let s2 = '小张'
        }
        console.log(s); // 小明
        //console.log(s2); // ReferenceError: s2 is not defined

        //4.不存在变量提升, var 变量是提前搜集声明，但let不会
        console.log(k); // undefined
        var k = 10;

        // console.log(age2); // ReferenceError: Cannot access 'age2' before initialization
        // let age2 = 20;

        //5.不影响作用域链
        let city = '上海市';
        {
            let city = '北京市';
            function printCity() {
                console.log(city);
            }
            printCity(); // 北京市
        }
        printCity(); // 北京市

        console.log("--------------2.const 用法--------------")
        //1.声明
        // const a;  2. 必须要赋值，否则报错

        // 3.常量一般大写
        const COUNTRY = '中国';

        // 4.不能修改
        //COUNTRY = '俄罗斯'; //TypeError: Assignment to constant variable.

        // 5.块级作用域
        {
            const AA = 10
        }
        // console.log(AA); // ReferenceError: AA is not defined

        var QTY = 12;
        {
            const QTY = 11;
            function printQty() {
                console.log(QTY);
            }
            printQty(); //11
        }
        printQty(); //11

        // 6.对数组或兑现的修改(引用类型的内容修改)，不算常量修改不算报错。
        const ARR = [1, 2, 3];
        ARR.push(4);
        console.log(ARR); // [1, 2, 3, 4]

        console.log("--------------3.变量结构赋值--------------");

        // ES6 允许按照一定的模式从数组或对象中提取值，对变量进行赋值，这被称为解构赋值。
        const ARR1 = ['小张', '小明', '小红'];
        let [zhang, ming = '', hong, wang = ''] = ARR1;
        console.log(zhang); //小张
        console.log(ming); //小明
        console.log(hong); //小红
        console.log(wang); // ''

        const OBJ = {
            name: '小明',
            age: 10,
            sayName: function () {
                console.log('我的名字是' + this.name);
            }
        };

        let { name, age, sayName } = OBJ;
        console.log(name); //小明
        console.log(age); //10
        sayName(); //我的名字是小明

        console.log("--------------4.模板字符串--------------");
        // 1.支持换行
        let str = `<ul>
            <ui>小明</ui>
            <ui>小张</ui>
            </ul>`;

        // 2.变量拼接
        let name3 = '小张';
        let strName3 = `${name3}是小学生。`
        console.log(strName3); //小张是小学生。

        console.log("--------------5.对象的简化写法--------------");

        let name4 = '小张';
        let age4 = 11;

        const student = {
            name4,
            age4,
            sayName4() {
                console.log('我的名字是' + this.name4);
            }
        };

        //和下面的一样的效果
        const student1 = {
            name4: name4,
            age4: name4,
            sayName4: function () {
                console.log('我的名字是' + this.name4);
            }
        };

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
