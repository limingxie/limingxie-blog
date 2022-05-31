---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_Promise(3)"
date: 2022-05-28T09:05:49+08:00
tags: [
    "vue",
    "Promise",
    "async",
    "await",

]
categories: [
    "Nodejs",
]
---

#### async

1. async函数的返回值是promise对象。
2. promise对象的结果由async函数执行的返回值决定。

#### await  

1. await右侧的表达式一般为promise对象。也可以是其他值。
2. await返回的是promise对象的成功的值。如果不是Promise对象直接是返回值。
3. await必须写在async函数中。但是async可以没有await。
4. await的promise失败了，就会抛出异常，需要用try catch捕获。

#### 简单实例

```js
//普通读取文件
fs.readFile('./test1.txt', (err, data1) => {
    if (err) {
        console.log(err);
    } else {
        fs.readFile('./test2.txt', (err, data2) => {
            if (err) {
                console.log(err);
            } else {
                fs.readFile('./test3.txt', (err, data3) => {
                    if (err) {
                        console.log(err);
                    } else {
                        let result = data1 + '\r\n' + data2 + '\r\n' + data3;
                        console.log(result);
                    }
                });
            }
        });
    }
});


//结合Promise和 async await
function readTest1(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test1.txt', (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    }) 
}

function readTest2(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test2.txt', (err, data) => {
            if (err) reject(err);
            resolve(data);
        });
    }) 
}

function readTest3(){
    return new Promise(function (resolve, reject) {
        fs.readFile('test3.txt', (err, data) => {
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
