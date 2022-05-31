---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_Promise(2)"
date: 2022-05-26T09:05:49+08:00
tags: [
    "Nodejs",
    "Promise",

]
categories: [
    "Nodejs",
]
---

跟着教程写了一遍Promise的自建方法。

```js
class Promise {
    constructor(executor) {
        //声明Prmise的两个属性
        this.PromiseState = 'pending';
        this.PromiseResult = null;
        this.callbacks = [];
        const self = this;
        function resolve(data) {
            //因为Promise是只能更改一次状态所以不是pending就retrun
            if (self.PromiseState != 'pending') {
                return;
            }
            self.PromiseState = 'fulfilled'
            self.PromiseResult = data;

            //使用setTime是为了做异步处理。
            setTimeout(() => {
                //链式处理callback
                self.callbacks.forEach(item => {
                    item.onResolved(data);
                });
            });
        }
        function reject(data) {
            //因为Promise是只能更改一次状态所以不是pending就retrun
            if (self.PromiseState != 'pending') {
                return;
            }
            self.PromiseState = 'rejected'
            self.PromiseResult = data;

            //使用setTime是为了做异步处理。
            setTimeout(() => {
                //链式处理callback
                self.callbacks.forEach(item => {
                    item.onRejected(data);
                })
            });
        }
        try {
            executor(resolve, reject);
        } catch (err) {
            reject(err);
        }

    }

    then(onResolved, onRejected) {
        const self = this;
        //判断传参中有没有onRejected就给默认值
        if (typeof onRejected !== 'function') {
            onRejected = reason => {
                throw reason;
            }
        }
        //判断传参中有没有onResolved就给默认值
        if (typeof onResolved !== 'function') {
            onResolved = value => value;
        }
        return new Promise((resolve, reject) => {
            function callback(type) {
                try {
                    let result = type(self.PromiseResult);
                    if (result instanceof Promise) {
                        result.then(value => {
                            resolve(value);
                        }, reason => {
                            reject(reason);
                        });
                    } else {
                        resolve(result);
                    }
                } catch (err) {
                    reject(err);
                }
            }

            if (this.PromiseState == 'fulfilled') {
                setTimeout(() => {
                    callback(onResolved);
                });
            }
            if (this.PromiseState == 'rejected') {
                setTimeout(() => {
                    callback(onRejected);
                });
            }
            if (this.PromiseState == 'pending') {
                this.callbacks.push({
                    onResolved: function () {
                        callback(onResolved);
                    },
                    onRejected: function () {
                        callback(onRejected);

                    }
                });
            }
        });
    }

    catch(onRejected) {
        return this.then(undefined, onRejected);
    }

    static resolve(value) {
        return new Promise((resolve, reject) => {
            if (value instanceof Promise) {
                value.then(v => {
                    resolve(v);
                }, r => {
                    reject(r);
                })
            } else {
                resolve(value);
            }
        })
    }

    static reject(reason) {
        return new Promise((resolve, reject) => {
            reject(reason);
        })
    }

    static all(promises) {
        return new Promise((resolve, reject) => {
            let count = 0;
            let arr = [];
            for (let i = 0; i < promises.length; i++) {
                promises[i].then(v => {
                    count++;
                    arr[i] = v;
                    if (count === promises.length) {
                        resolve(arr);
                    }
                }, r => {
                    reject(r);
                })
            }
        });
    }

    static race(prmises) {
        return new Promise((resolve, reject) => {
            for (let i = 0; i < promises.length; i++) {
                promises[i].then(v => {
                    resolve(v);
                }, r => {
                    reject(r);
                });
            }
        });
    }
}

```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
