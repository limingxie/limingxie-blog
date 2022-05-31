---
author: "li_mingxie"
title: "【es11】es11的新特性整理(13)"
date: 2022-05-21T09:05:49+08:00
tags: [
    "es11",
    "es9",
]
categories: [
    "Emas",
]
---

这里整理了ES11新增的特性。  

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("-------------- 私有属性 --------------");
        {
            class Person {
                //公有属性
                name;
                //私有属性
                #age;
                #weight;

                //构造方法
                constructor(name, age, weight) {
                    this.name = name;
                    this.#age = age;
                    this.#weight = weight;
                }

                printPerson() {
                    console.log(girl.name);
                    console.log(girl.#age);
                    console.log(girl.#weight);
                }
            }

            const girl = new Person('小红', 18, '50kg');
            console.log(girl.name);
            //console.log(girl.#age); // SyntaxError: Private field '#age' must be declared in an enclosing class
            girl.printPerson(); // 小红 18 50kg
        }

        console.log("-------------- Promise.allSettled --------------");
        {
            const p1 = new Promise((resolve, reject) => {
                setTimeout(() => {
                    resolve('商品数量 - 1');
                }, 1000);
            });

            const p2 = new Promise((resolve, reject) => {
                setTimeout(() => {
                    resolve('商品数量 - 2');
                }, 1000);
            });

            const p3 = new Promise((resolve, reject) => {
                setTimeout(() => {
                    reject('没有商品');
                }, 1000);
            });

            //调用 allsettled 方法，返回一个Promise对象，该对象的状态取决于所有Promise对象的状态。
            const result = Promise.allSettled([p1, p2, p3]);
            console.log(result);
            /*
                Promise {<pending>}
                    [[Prototype]]: Promise
                    [[PromiseState]]: "fulfilled"
                    [[PromiseResult]]: Array(3)
                        0: {status: 'fulfilled', value: '商品数量 - 1'}
                        1: {status: 'fulfilled', value: '商品数量 - 2'}
                        2: {status: 'rejected', reason: '没有商品'}
                        length: 3
                        [[Prototype]]: Array(0)
            */

            //Promise.all 只有全部成功了才返回成功， allsettled则不是
            const result1 = Promise.all([p1, p2]);
            console.log(result1);
            /*
                Promise {<pending>}
                    [[Prototype]]: Promise
                    [[PromiseState]]: "fulfilled"
                    [[PromiseResult]]: Array(2)
                        0: "商品数量 - 1"
                        1: "商品数量 - 2"
                        length: 2
                        [[Prototype]]: Array(0)
            */

            const result2 = Promise.all([p1, p2, p3]);
            console.log(result2);
            /*
            Promise {<pending>}[[Prototype]]: Promise
                [[PromiseState]]: "rejected"
                [[PromiseResult]]: "没有商品"
                
                Uncaught (in promise) 没有商品
            */
        }

        console.log("-------------- 可选链操作符 --------------");
        {
            function main(config) {
                const dbHost = config && config.db && config.db.host;
                console.log(dbHost)
            }
            main({
                db: {
                    host: '127.0.0.1',
                    username: 'root'
                },
                cache: {
                    host: '192.168.1.1',
                    username: 'admin'
                }
            }); //127.0.0.1

            function main1(config) {
                const dbHost = config.db.host;
                console.log(dbHost)
            }
            // main1(); // TypeError: Cannot read properties of undefined

            function main2(config) {
                const dbHost = config?.db?.host;
                console.log(dbHost)
            }
            main2(); //undefined

            main2({
                db: {
                    host: '127.0.0.1',
                    username: 'root'
                },
                cache: {
                    host: '192.168.1.1',
                    username: 'admin'
                }
            }); //127.0.0.1
        }

        console.log("-------------- BigInt --------------");
        {
            let n = 123;
            console.log(BigInt(n)); //123n
            //console.log(BigInt(12.2)); //The number 12.2 cannot be converted to a BigInt because it is not an integer
            console.log(Number.MAX_SAFE_INTEGER); //9007199254740991
            //BigInt 运算需要和BigInt进行运算
            console.log(BigInt(Number.MAX_SAFE_INTEGER) + BigInt(10)); //9007199254741001n
        }
        console.log("-------------- globalThis --------------");
        {
            console.log(globalThis); //Window {...}
            console.log(globalThis === window); //true
            console.log(globalThis === global); //true
        }
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
