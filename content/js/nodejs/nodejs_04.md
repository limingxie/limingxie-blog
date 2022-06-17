---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_Promise(1)"
date: 2022-05-25T09:05:49+08:00
tags: [
    "Nodejs",
    "Promise",

]
categories: [
    "Nodejs",
]
---

## 1.Promiose 是什么？

Promise是JS中进行异步编程的解决方案。可以有效的避免了之前版本的回调地狱方式。  
Promise是一个构造函数。  
Promise对象用来封装一个异步操作并可以获取成功或失败的结果值。  <!--more-->

简单的看看Promise和传统的回调方式：  

```js
/*
    编写一个抽奖程序，抽奖的过程是：  
    取一个随机数1~100，1~30为中奖，31~100为没中奖。  
*/
function rand(m, n) {
    return Math.ceil(Math.random() * (n - m + 1) + m - 1);
}
console.log('-----------传统方式-----------');
{
    function raffle() {
        setTimeout(() => {
            let a = rand(1, 100);
            if (a < 30) {
                console.log(`${a} | 恭喜你中奖了, 奖金是10万元。`);
            } else {
                console.log(`${a} | 很遗憾，您没中奖，再接再厉！`);
            }
        }, 1000);
    }

    raffle();
}

console.log('-----------Promise方式-----------');
{
    //new Promise的时候需要制定一个回调函数，这个函数的参数是resolve和reject。
    //resolve是成功时调用，reject是失败时调用。
    const p = new Promise((resolve, reject) => {
        setTimeout(() => {
            let a = rand(1, 100);
            if (a < 30) {
                resolve(a);
            } else {
                reject(a);
            }
        }, 1000);
    });

    //then也有2个回调函数，成功第一个，失败第二个。
    p.then((value) => {
        console.log(`${value} | 恭喜你中奖了, 奖金是10万元。`);
    }, (reason) => {
        console.log(`${reason} | 很遗憾，您没中奖，再接再厉！`)
    });
}
```

## 2.什么时候用Promise？

简单的说异步编程的时候会使用Promise。  
一般的异步编程有: 1.fs文件读取 2.http请求 3.DataBase操作 4.定时器等等...

下面我们看看简单的读取文件的方法:  

```js
const fs = require('fs');

//普通的回调函数的方式
fs.readFile('./test.txt', (err, data) => {
    if (err) throw err;
    console.log(data.toString());
});

//promise的方式
const p = new Promise(function (resolve, reject) {
    fs.readFile('./test.txt', (err, data) => {
        if (err) reject(err);
        resolve(data);
    });
});

p.then(function (value) {
    console.log(value.toString());
}, function (reason) {
    console.log(reason);
});
```

这么一看Promise方式的代码好像更复杂....^^;;  
我们看看Promise的有点是什么？  

## 3.Promise优点

1. 指定回调函数方式更加灵活。
以前必须写在回调函数里面  
现在可以启动任务，返回Promise对象，然后给promise对象绑定回调函数。  

2. 支持链式调用，可以解决回调地狱
以前的回调地狱，现在可以把回调函数放在Promise对象的then方法里面。  

第一个有点可能没什么太大的感触，但是回调地狱是没有Promise的链式调用之前是每个开发者的噩梦。  
我们举一个简单的例子看看:  

```js
const fs = require('fs');

/*
    想读取 test1.txt test2.txt test3.txt 三个文件，并且把它们的内容都读取出来。
    普通的回调方式，很容易就会发生回调地狱，如果有4个甚至更多文件，这个地狱就更深了...^^;;
*/

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


//使用Promise读取文件, 虽然看着比普通的方式更复杂，
//但是他解决的回调地狱的问题，以及提高了代码可读性。
const p = new Promise(function (resolve, reject) {
    fs.readFile('./test1.txt', (err, data) => {
        if (err) reject(err);
        resolve(data);
    });
});

p.then(function (value) {
    return new Promise((resolve, reject) => {
        fs.readFile('./test2.txt', (err, data) => {
            if (err) reject(err);
            resolve([value, data]);
        });
    });
}, function (reason) {
    console.log(reason);
}).then(function (value) {
    return new Promise((resolve, reject) => {
        fs.readFile('./test3.txt', (err, data) => {
            if (err) reject(err);
            value.push(data);
            resolve(value);
        });
    });
}, function (reason) {
    console.log(reason);
}).then((value) => {
    console.log(value.join('\r\n'));
});

```

## 4.PromiseState

Promise对象的状态有两种：pending和fulfilled。  
1.Pending: 等待中  
2.Resolved(fulfilled): 成功  
3.Rejected: 失败  

Pending: 等待中 => Resolved: 成功  
Pending: 等待中 => Rejected: 失败  

Resolved 方法里 throw 也可以把Promise状态改成 失败  

## 5.PromiseResult

Promise 对象值 Promiseresult  
保存异步任务成功或失败的结果值  
resovle, reject 两个方法能修改这个值  

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script>
        const p = new Promise((resolve, reject) => { });
        console.log(p); 
        /*
            [[Prototype]]: Promise
            [[PromiseState]]: "pending"
            [[PromiseResult]]: undefined
        */

        const p1 = new Promise((resolve, reject) => {
            resolve('OK');
        });
        console.log(p1); //Promise {<fulfilled>: 'OK'}
        /*
            [[Prototype]]: Promise
            [[PromiseState]]: "fulfilled"
            [[PromiseResult]]: "OK"
        */

        const p2 = new Promise((resolve, reject) => {
            reject('fail');
        });
        console.log(p2);
        /*
            [[Prototype]]: Promise
            [[PromiseState]]: "rejected"
            [[PromiseResult]]: "fail"
        */
        
        const p3 = new Promise((resolve, reject) => {
                throw ("fail");
        });
        console.log(p3);
        /*
            [[Prototype]]: Promise
            [[PromiseState]]: "rejected"
            [[PromiseResult]]: "fail"
        */
            
    </script>
</head>

<body>
</body>

</html>
```

## 6.Promise API

#### 构造函数 Promise(executor)

(resolve, reject) => {  
    //这里的函数是同步调用的，不会进队列  
}
then(onFulfilled, onRejected) //指定回调，成功的话执行前面的方法，失败执行后面的方法  
catch(onRejected) //执行失败时，调用的方法。  
finally(onFinally) //无论成功失败，都会执行。  

//如果传递参数是非Promise对象，返回结果为成功的的Promise对象，如果是参数是Promise对象，则看Promise本身的状态。  
let p1 = Promise.resolve(111);  
console.log(p1);  

//返回失败的Promise对象，无论是成功的Promise对象也是返回失败的Promise对象。  
let p2 = Promise.reject(222);  
console.log(p2);  

#### Promise.all

//成功返回成功Promise数组对象，失败返回其中失败Promise对象  
const result = Promise.all(p1,p2,p3);  
console.log(result);  

#### Promise.race

//谁先改变状态，返回他的Promise结果  
//成功返回成功Promise数组对象，失败返回其中失败Promise对象  
const result = Promise.all(p1,p2,p3);  
console.log(result);  

#### util.promisify

```js
const util = require('util');
const fs = require('fs');

//使用promisify返回一个新的函数
let myReadFile = util.promisify(fs.readFile);

myReadFile('./aa.txt').then(value=>{
    console.log(value.toString());
}, reason=>{
    console.log(reason);
})
```

## 7.附加整理

#### Promise 调用

```js
let p = new Promise((resolve, reject) => {
    setTimeout(()=> {
        resolve('OK');
    },2000)
})

p.then(value => {
    console.log(value);
}, reason => {
    console.log(reason);
}).then(value =>{
    return new Promise(()=>{});
}).then(value =>{
    console.log('111');
}).catch(err =>{
    console.warn(err);
});
```

#### 自定义读取文件方法

```js
function myReadFile(path){
    return new Promise((resolve, reject)=>{
        require('fs').readFile(path, (err,data)=>{
            if(err) reject(err);
            resolve(data);
        });
    })
}

myReadFile('./test.txt')
.then(value=>{
    console.log(value.toString());
}, reason=>{
    console.log(reason);
}) 
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
