---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_模块化(10)"
date: 2022-05-18T09:05:49+08:00
tags: [
    "es6",
    "export",
    "require",
]
categories: [
    "Emas",
]
---

这里整理了模块化相关的内容。  

**export**: 规定模块对外接口。  
**import**: 输入引用其他模块提供的功能。  <!--more-->

#### test.js

```js
const m1 = require("./m1.js");
const {name, school} = require("./m2.js");

console.log(m1.name); //小明
console.log(m1.school); //中学
m1.teach(); //教中学

console.log(name); //小红
console.log(school); //大学

```

#### m1.js

```js
module.exports = {
    name: "小明",
    school: "中学",
    teach: function () {
        console.log("教中学");
    }
}
```

#### m2.js

```js
module.exports = {
    name: "小红",
    school: "大学"
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
