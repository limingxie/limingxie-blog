---
author: "li_mingxie"
title: "【es8笔记】es8的新特性_async_await(11)"
date: 2022-05-19T09:05:49+08:00
tags: [
    "es8",
    "async",
    "await",
]
categories: [
    "Emas",
]
---

这里整理了ES8里的async和await。  

async和await可以让一步操作跟同步代码一样。<!--more-->

#### async

1. async函数的返回值是promise对象。
2. promise对象的结果由async函数执行的返回值决定。

#### import  

1. await必须写在async函数中。
2. await右侧的表达式一般为promise对象。
3. await返回的是promise对象的结果。
4. await的promise失败了，就会抛出异常，需要用try catch捕获。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("-------------- async --------------");
        {
            async function fn() {
                return '小明'; //返回一个 Promise, 不是promise 都是成功的promise
            }
            let f = fn();
            console.log(f); //Promise {<fulfilled>: '小明'}

            //返回错误的Promise对象
            async function fn1() {
                return new Promise(function (resolve, reject) {
                    return new Error('报错');
                })
            }
            let f1 = fn1();
            console.log(f1); //Promise {<pending>}

            //正常返回错误的语法
            async function fn2() {
                return new Promise(function (resolve, reject) {
                    reject('错误');
                })
            }
            let f2 = fn2();
            console.log(f2); //Promise {<pending>}} Uncaught (in promise) 错误

            //返回的是promise对象 所以后续可以加then做处理。
            f2.then(value => {
                console.log(value);
            }, reason => {
                console.log(reason);
            })
        }

        console.log("-------------- await --------------");
        {
            //await必须在async函数中使用， async未必有await

            const p = new Promise((resovle, reject) => {
                resovle('处理成功');
            })

            async function fn() {
                let value = await p;
                console.log(value);
            }

            fn(); //处理成功

            const p1 = new Promise((resovle, reject) => {
                reject('处理失败');
            })

            async function fn1() {
                try {
                let value = await p1;
                console.log(value);
                }catch (err) {
                    console.log(err); //Uncaught (in promise) 处理失败
                }
            }

            fn(); //处理成功
        }

    </script>
</body>

</html>
```

#### 练习读取文件

```js
const fs = require('fs');

function readTest1(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test.md', (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    }) 
}

function readTest2(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test1.md', (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    }) 
}

function readTest3(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test2.md', (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    }) 
}

async function main(){
    let test1 = await readTest1();
    let test2 = await readTest2();
    let test3 = await readTest3();
    console.log(test1.toString());
    console.log(test2.toString());
    console.log(test3.toString());
}

main();
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
