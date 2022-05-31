---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_Set_Map(7)"
date: 2022-05-15T09:05:49+08:00
tags: [
    "es6",
    "Set",
    "Map",
]
categories: [
    "Emas",
]
---

这里整理了Set和Map相关的内容。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------Set 集合--------------");
        /*
        类似于数组，但是成员的值都是唯一。集合实现了iterator接口，所以可以用for...of循环遍历。
        集合常用的方法 size, add ,delete, has
        */
        let s = new Set();
        let s1 = new Set(['小明', '小红', '小刚']);
        console.log(s1);//Set(3) {'小明', '小红', '小刚'}
        console.log(s1.size); //3
        s1.add('小白');
        console.log(s1); //Set(4) {'小明', '小红', '小刚', '小白'}
        s1.add('小白');
        console.log(s1); //Set(4) {'小明', '小红', '小刚', '小白'}

        s1.delete('小白');
        console.log(s1);//Set(3) {'小明', '小红', '小刚'}

        s1.clear();
        console.log(s1); //Set(0) {size: 0}

        //Set 实践
        //1.数组去重
        let arr = [1, 2, 3, 4, 5, 3, 2, 1];
        let s3 = [... new Set(arr)]
        console.log(s3); // (5) [1, 2, 3, 4, 5]

        //2.交集
        let arr2 = [2, 4, 7, 8];
        let s4 = [... new Set(arr)].filter(item => {
            let s5 = new Set(arr2);
            if (s5.has(item)) {
                return true;
            } else {
                return false;
            }
        });

        console.log(s4); //(2) [2, 4]

        //简化
        let s5 = [...new Set(arr)].filter(item => new Set(arr2).has(item));
        console.log(s5); //(2) [2, 4]

        //3.并集
        let s6 = new Set([...arr,...arr2]);
        console.log(s6); //Set(7) {1, 2, 3, 4, 5, 7, 8}

        //4.差集
        let s7 = [...new Set(arr)].filter(item => !new Set(arr2).has(item));
        console.log(s7); //(3) [1, 3, 5]

        console.log("-------------- Map --------------");
        /*
        Map是一个数据结构，键值对的集合。键不限于字符串，各种类型的值（包括对象）都可以当作键。
        也实现了iterator接口。可以使用扩展运算符和 for of
        常用方法： size, set, get, has, clear
        */

        let m = new Map();
        let m1 = new Map([['小明', '男'], ['小红', '女'], ['小刚', '男']]);

        m.set('name', '小明');
        m.set('age', 18);
        m.set('run', function () {
            console.log('running');
        });

        let key = {
            city: '北京'
        }

        m.set(key, ['朝阳','海淀', '顺义']);

        console.log(m); //Map(4) {name: "小明", age: 18, run: ƒ, {city: "北京"}: Array(3)}

        m.delete('name');
        console.log(m.get('run'));

        console.log(m.size); //3

        for(let v of m) {
            console.log(v);
        }
        //(2) ['age', 18]
        // ['run', ƒ]
        //(2) [{…}, Array(3)]

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
