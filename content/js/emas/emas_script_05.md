---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_迭代器(5)"
date: 2022-05-13T09:05:49+08:00
tags: [
    "es6",
    "iterator",
    "yield",
]
categories: [
    "Emas",
]
---

这里整理了迭代器Iterator相关的内容。<!--more-->

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------迭代器Iterator--------------");
        /*
        迭代器Iterator是一种接口，可以让任何数据结构（包括数组、对象）都可以迭代。
        */
        const arr = ['小明', '小红', '小白'];

        // 1.for...of循环 Symbol.Iterator
        /*
        1.创建一个指针对象，指向当前初始位置
        2.第一次调用对象的next方法。指针自动指向第一个成员
        3.接下来不断的调用next方法，直到指针指向的成员不再存在
        4.每调用next方法返回一个包含value和done的对象
        */
        for (let i of arr) {
            console.log(i);
        }
        // 小明 小红 小白

        let iterator = arr[Symbol.iterator]();
        console.log(iterator); //Array Iterator {}
        console.log(iterator.next()); //{value: '小明', done: false}
        console.log(iterator.next()); //{value: '小明', done: false}
        console.log(iterator.next()); //{value: '小白', done: false}
        console.log(iterator.next()); //{value: undefined, done: true}

        for (let i in arr) {
            console.log(i);
        }
        // 0 1 2

        // 2.自定义遍历数据

        /*
        const obj = {
            name: '一班',
            stu: ['小明', '小红', '小刚']
        };

        这么遍历会报错
        for(let i of obj) {
            console.log(i);
        }
        */
        const obj = {
            name: '一班',
            stu: ['小明', '小红', '小白'],
            [Symbol.iterator]: function () {
                let index = 0;
                let self = this;
                return {
                    next: function () {
                        if (index < self.stu.length) {
                            let result = {
                                value: self.stu[index],
                                done: false
                            };
                            index++;
                            return result;
                        } else {
                            return {
                                value: self.stu[index],
                                done: true
                            }
                        }
                    }
                }
            }
        };

        for(let i of obj) {
            console.log(i);
        }
        // 小明 小红 小白


        console.log("--------------2.生成器--------------");
        // 2.生成器 Generator 异步编程 存回调函数

        function * gen() {
            console.log('print generator');
        }

        let iterator1 = gen();
        console.log(iterator1); //gen {<suspended>}
        iterator1.next(); // print generator

        function * gen2() {
            yield '第一个';
            yield '第二个';
            yield '第三个';
        }

        let iterator2 = gen2();
        console.log(iterator2.next()); //{value: '第一个', done: false}
        console.log(iterator2.next()); //{value: '第二个', done: false}
        console.log(iterator2.next()); //{value: '第三个', done: false}
        console.log(iterator2.next()); //{value: undefined, done: true}

        function * gen3(arg) {
            console.log(arg)
            let str = yield '第一个';
            console.log('1:',str);
            let str1 = yield '第二个';
            console.log(str1);
            yield '第三个';
        }

        let iterator3 = gen3("aa");
        console.log(iterator3.next("bb")); // aa {value: '第一个', done: false}
        console.log(iterator3.next("cc")); // cc {value: '第二个', done: false}
        console.log(iterator3.next()); //{value: '第三个', done: false}
        console.log(iterator3.next()); //{value: undefined, done: true}

        // 3.异步编程
        // setTimeout(() => {
        //     console.log(111);
        //     setTimeout(() => {
        //         console.log(222);
        //         setTimeout(() => {
        //             console.log(333);
        //         }, 3000);
        //     }, 2000);
        // }, 1000);

        function one(){
            setTimeout(() => {
                console.log(111);
                iterator4.next();
            }, 1000);
        };
        function two(){
            setTimeout(() => {
                console.log(222);
                iterator4.next();
            }, 2000);
        };
        function three(){
            setTimeout(() => {
                console.log(333);
                // iterator4.next();
            }, 3000);
        };

        function * gen4(){
            yield one();
            yield two();
            yield three();
        }

        let iterator4 = gen4();
        // iterator4.next();

        // 4.联系

        function getUsers(){
            setTimeout(()=>{
                let user = '用户信息';
                iterator5.next(user);
            },1000);
        }
        function getOrders(){
            setTimeout(()=>{
                let order = '订单信息';
                iterator5.next(order);
            },1000);
        }
       function  getGoods(){
           setTimeout(()=>{
                let goods = '商品数量';
                iterator5.next(goods);
              },1000);
        }

        function * gen5(){
           let user =  yield getUsers();
           console.log(user);
           let order = yield getOrders();
           console.log(order);
           let goods = yield getGoods();
           console.log(goods);
        }

        let iterator5 = gen5();
        iterator5.next();

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
