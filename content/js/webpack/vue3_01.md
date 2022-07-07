---
author: "li_mingxie"
title: "【webpack笔记】webpack_初建项目(01)"
date: 2022-07-13T23:55:49+08:00
tags: [
    "webpack",
]
categories: [
    "webpack",
]
---

这一篇开始记录了webpack相关的内容。  
先创建一个项目用webpack打包看看。  
<!--more-->

## 1.项目结构

先按照以下目录创建文件。  

```bash
webpack_code # 项目根目录（所有指令必须在这个目录运行）
    └── src # 项目源码目录
        ├── js # js文件目录
        │   ├── count.js
        │   └── sum.js
        └── main.js # 项目主文件
```

## 2.功能代码

#### count.js

```js
export default function count(x, y) {
  return x - y;
}
```

#### sum.js

```js
export default function sum(...args) {
  return args.reduce((p, c) => p + c, 0);
}
```

#### main.js

```js
import count from "./js/count";
import sum from "./js/sum";

console.log(count(2, 1));
console.log(sum(1, 2, 3, 4));
```  

## 3.初始化webpack

到根目录执行以下命令

```bash
npm init -y

#下载依赖
npm i webpack webpack-cli -D
```

## 4.使用webpack打包

开发模式：仅能编译JS中的ES Module语法  
生产模式：能编译JS中的ES Module语法，还能压缩JS代码  

默认Webpack会将文件打包输出到dist目录下  

```bash
#开发模式
npx webpack ./src/main.js --mode=development

#生产模式
npx webpack ./src/main.js --mode=production
```

* npx webpack: 是用来运行本地安装 Webpack 包的。  
* ./src/main.js: 指定 Webpack 从 main.js 文件开始打包，不但会打包 main.js，还会将其依赖也一起打包进来。  
* --mode=xxx：指定模式（环境）  

默认Webpack会将文件打包输出到dist目录下。

下一篇开始整理除打包JS资源以外的方式。

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
