---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_模块化"
date: 2022-05-23T09:05:49+08:00
tags: [
    "Nodejs",
    "module",
    "exports",
    "require",
]
categories: [
    "Nodejs",
]
---

这篇简单的整理了nodejs的模块化的内容。  

* 每一个js文件是一个模块。每个js文件其实是被一个函数包裹着  
* 引用文件需要require  
* 提供接口需要用export  

具体的内容直接看实例:  

**md.js**

```js
module.exports = {
    x: 10,
    fn: () => { console.log(arguments.callee + '') },
}

// exports.z = 30;

var y = 20;
console.log(y);

```

**test.js**

```js
const md = require('./md.js');
md.fn();


//所有的模块都是函数，通过 console.log(arguments.callee + '') 方式可以查看函数的值。
/*
20          => 执行了md.js里的 console.log(y);
function (exports, require, module, __filename, __dirname) {      =>执行了 console.log(arguments.callee + '')
module.exports = {
    x: 10,
    fn: () => { console.log(arguments.callee + '') },
}

var y = 20;
console.log(y);
}
*/


/*
模块函数的参数，每个模块文件node都会包一个函数，而且传这4个参数
exports: 通过该对象是对外报漏属性和方法
require: 函数，引用模块
module: 当前本身模块，exports是module的属性
__filename:当前模块路径
__dirname: 当前模块文件夹路径
*/

var aa =10; //这是局部变量
bb = 10; //这是全局变量

console.log(global.aa); //undefined
console.log(global.bb); //10


/*
exports 和 module.exports的区别：
exports是模块的属性，module.exports是模块的方法，module.exports是模块的属性和方法的集合。
*/
var obj = {}
obj.a = {};
var a = obj.a;
console.log(a == obj.a); //true

a.name = '小明';
console.log(obj.a.name); //小明

a = new Object();
console.log(a.name); //undefined
console.log(obj.a.name); //小明

{
    //这是改的是module的属性的值
    module.exports = {

    }
    //这是改的是对象，直接新建对象值。所以这种方式无效,只能 exports.xxx = 
    exports = {

    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
