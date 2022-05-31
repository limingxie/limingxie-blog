---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_Promise(6)"
date: 2022-05-14T09:05:49+08:00
tags: [
    "es6",
    "promise",
    "yield",
]
categories: [
    "Emas",
]
---

这里整理了Promise相关的内容。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("--------------Promise--------------");
        /*
        Promise是ES6引入的异步编程的新解决方案。IO相关的 文件fs, request, 数据库连接等等...
        Promise是一个构造函数。
        */
        // resolve 成功后调用 它会返回true,  reject 失败后调用 它会返回false
        const p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                let data = '数据库数据';
                resolve(data);
                // let err = '数据读取失败';
                //reject(err);
            }, 1000);
        });
        p.then(function (value) { // 成功后执行
            console.log(value);
        }, function (reason) { //失败后执行
            console.log('出错了:', reason);
        });

        {
            console.log('---------------------------');
            //创建promise对象
            const p1 = new Promise(function (resolve, reject) {
                setTimeout(()=>{
                    resolve('成功了');
                    reject('出错了');
                }, 1000);
            });

            //调用 then 方法的返回结果是Promise对象，由回调函数的结果决定
            //1.如果返回结果是非Promise对象，状态是成功，返回为对象成功的值
            const result = p1.then(function (value) {
                console.log('成功了:', value);
                // 不写 return 会返回undefined 等于成功。
                return new Promise((resolve,reject)=>{
                    resolve('ok'); //上层的Promise也是成功
                    //reject('err'); //上层的Promise也是失败
                })
            }, function (reason) {
                console.log('失败了:', reason);
            });

            //链式调用, 避免回调地狱
            p1.then(()=>{}).then(()=>{}).then();
            
        }
        console.log("---------Promise Ajax操作---------");
        /*
        //普通调用
        {
            //1.创建对象
            const xhr = new XMLHttpRequest();

            //2.初始化
            xhr.open("GET", "https://baidu.com");

            //3.发送请求
            xhr.send();

            //4.绑定事件
            xhr.onreadystatechange = function () {
                //判断
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
                        console.log(xhr.response);
                    } else {
                        console.log(xhr.status);
                    }
                }
            }

        }

        //Promise
        {
            const p = new Promise(function (resolve, reject) {
                //1.创建对象
                const xhr = new XMLHttpRequest();

                //2.初始化
                xhr.open("GET", "https://baidu.com");

                //3.发送请求
                xhr.send();

                //4.绑定事件
                xhr.onreadystatechange = function () {
                    //判断
                    if (xhr.readyState === 4) {
                        if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
                            resolve(xhr.response);
                        } else {
                            reject(xhr.status);
                        }
                    }
                }
            });

            p.then(function (value) {
                console.log(value);
            }, function (reason) {
                console.log(reason);
            })

        }
        */

    </script>
</body>

</html>
```

#### 练习

**读取文件**

```js
const fs = require('fs');

fs.readFile('test.md', (err, data) => {
    if (err) throw err;
    console.log(data.toString());
});

const p = new Promise(function (resolve, reject) {
    fs.readFile('test.md', (err, data) => {
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

**连续读取文件**

```js
const fs = require('fs');

//普通读取文件
fs.readFile('test.md', (err, data1) => {
    fs.readFile('test1.md', (err, data2) => {
        fs.readFile('test2.md', (err, data3) => {
            let result = data1 + '\r\n' + data2 + '\r\n' + data3;
            console.log(result);
        });
    });
});

//使用Promise读取文件
const p = new Promise(function (resolve, reject) {
    fs.readFile('test.md', (err, data) => {
        if (err) reject(err);
        resolve(data);
    });
});

p.then(function (value) {
    return new Promise((resolve, reject) => {
        fs.readFile('test1.md', (err, data) => {
            if (err) reject(err);
            resolve([value, data]);
        });
    });
}, function (reason) {
    console.log(reason);
}).then(function (value) {
    return new Promise((resolve, reject) => {
        fs.readFile('test2.md', (err, data) => {
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

**错误处理**

```js
//处理错误
const p1 = new Promise(function (resolve, reject) {
    setTimeout(() => {
        reject('出错了');
    })
});

p1.then(function (value) { }, function (reason) {
    console.log(reason);
});

//使用catch处理错误
const p2 = new Promise(function (resolve, reject) {
    setTimeout(() => {
        reject('出错了');
    })
});

p2.catch(function (reason) {
    console.log(reason);
})
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
