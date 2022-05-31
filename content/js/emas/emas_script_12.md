---
author: "li_mingxie"
title: "【es8,es9,es10笔记】es8,es9的新特性_扩展(12)"
date: 2022-05-20T09:05:49+08:00
tags: [
    "es8",
    "es9",
]
categories: [
    "Emas",
]
---

这里整理了ES8 对象方法扩展, ES9 扩展运算符和rest参数 和ES10 扩展方法。  

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("-------------- ES8 对象方法扩展 --------------");
        {
            const school = {
                name: '大学',
                cities: ['北京', '上海', '深圳'],
                students: ['小明', '小红', '小黑']
            }
            console.log(Object.keys(school)); //Array(3) ["name", "cities", "students"]
            console.log(Object.values(school)); //(3) ['大学', Array(3), Array(3)]
            //key values 
            console.log(Object.entries(school)); //(3) [Array(2), Array(2), Array(2)]
            const map = new Map(Object.entries(school));
            console.log(map.get('name')); // 大学

            //对象属性描述对象
            console.log(Object.getOwnPropertyDescriptors(school));//{name: {…}, cities: {…}, students: {…}}

            const obj = Object.create(null, {
                name: {
                    //设置值
                    value: '小明',
                    writable: true,
                    configurable: true,
                    enumerable: true
                }
            });
        }

        console.log("-------------- ES9 扩展运算符和rest参数 --------------");
        {
            function connect({ host, port, ...user }) {
                console.log(host);
                console.log(port);
                console.log(user);
            }

            connect({
                host: 'localhost',
                port: 8080,
                username: 'root',
                password: '1234',
                type: 'mysql'
            });

            //localhost
            //8080
            //{username: 'root', password: '1234', type: 'mysql'}

            const user1 = {
                name1: '小明',
                age1: 18,
                weight1: '60kg'
            }

            const user2 = {
                name1: '小红',
                age1: 15,
                weight2: '40kg'
            }

            const user3 = {
                name3: '小刚',
                age3: 17,
                weight3: '60kg'
            }

            const result = { ...user1, ...user2, ...user3 }; //{name1: '小红', age1: 15, weight1: '60kg', weight2: '40kg', name3: '小刚', …}
            console.log(result);
        }
        console.log("-------------- ES10 扩展方法 --------------");

        {
            //二维数组转换对象
            const result = Object.fromEntries([
                ['name', '小明'],
                ['age', 18],
                ['weight', '60kg']
            ]);

            console.log(result); //{name: '小明', age: 18, weight: '60kg'}

            const m = new Map();
            m.set('name', '小明');
            const result1 = Object.fromEntries(m);
            console.log(result1); //{name: '小明'}

            //对象转换二维数组
            const arr = Object.entries({ name: '小明', age: 18, weight: '60kg' });
            console.log(arr); //(3) [Array(2), Array(2), Array(2)]
        }

        {
            //trimStart() trimEnd()
            let str = '  hello world  ';
            console.log(str.trimStart()); //hello world
            console.log(str.trimEnd()); //  hello world
        }

        {
            //flat() flatMap()
            //多维数组转化为低位数字
            const arr = [1, 2, 3, [4, [5, 6]]];
            console.log(arr.flat());//(5) [1, 2, 3, 4, Array(2)]
            console.log(arr.flat(2));//(6) [1, 2, 3, 4, 5, 6]

            const arr1 = [1, 2, 3, [4, 5, 6]];
            const result = arr1.map(item => item * 10);
            console.log(result);//(4) [10, 20, 30, NaN]
            const result1 = arr1.map(item => [item * 10]);
            console.log(result1);//(4) [Array(1), Array(1), Array(1), Array(1)]
            const result2 = arr1.flatMap(item => [item * 10]);
            console.log(result2);//[10, 20, 30, NaN]
        }

        {
            //Symbol.prototype.description
            const s = Symbol('小明');
            console.log(s.description); //小明
        }

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
