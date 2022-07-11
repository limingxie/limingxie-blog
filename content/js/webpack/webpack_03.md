---
author: "li_mingxie"
title: "【webpack笔记】webpack_Eslint_Babel(03)"
date: 2022-07-15T23:55:49+08:00
tags: [
    "webpack",
]
categories: [
    "webpack",
]
---

这一篇整理了Eslint和Babel相关的内容。  
<!--more-->

* Eslint处理代码格式问题
* Babel处理js兼容性问题

## 1.Eslint

是用来检测js和jsx语法的工具  
配置文件由很多种写法

.eslintrc.js 配置文件

```js
module.exports = {
  // 解析选项
  parserOptions: {
    ecmaVersion: 6, // ES 语法版本
    sourceType: "module", // ES 模块化
    ecmaFeatures: { // ES 其他特性
      jsx: true // 如果是 React 项目，就需要开启 jsx 语法
    }
  },
  // 具体检查规则
  rules: {
    semi: "error", // 禁止使用分号
    'array-callback-return': 'warn', // 强制数组方法的回调函数中有 return 语句，否则警告
    'default-case': [
      'warn', // 要求 switch 语句中有 default 分支，否则警告
      { commentPattern: '^no default$' } // 允许在最后注释 no default, 就不会有警告了
    ],
    eqeqeq: [
      'warn', // 强制使用 === 和 !==，否则警告
      'smart' // https://eslint.bootcss.com/docs/rules/eqeqeq#smart 除了少数情况下不会有警告
    ],
  },
  // 继承其他规则
  extends: [],
  // ...
  // 其他规则详见：https://eslint.bootcss.com/docs/user-guide/configuring
};
```

[Eslint 官方的规则](https://eslint.bootcss.com/docs/rules/) ：eslint:recommended  
[Vue Cli 官方的规则](https://github.com/vuejs/vue-cli/tree/dev/packages/@vue/cli-plugin-eslint)：plugin:vue/essential  

#### 1.1 下载包  

```bash
npm i eslint-webpack-plugin eslint -D
```

#### 1.2 定义Eslint

配置文件 .eslintrc.js  

#### 1.3 修改 js 文件代码 main.js  

```js
import count from "./js/count";
import sum from "./js/sum";
// 引入资源，Webpack才会对其打包
import "./css/iconfont.css";
import "./css/index.css";
import "./less/index.less";
import "./sass/index.sass";
import "./sass/index.scss";
import "./styl/index.styl";

var result1 = count(2, 1);
console.log(result1);
var result2 = sum(1, 2, 3, 4);
console.log(result2);
```

#### 1.4 配置webpack.config.js  

```js
const path = require("path");
const ESLintWebpackPlugin = require("eslint-webpack-plugin");

module.exports = {
  entry: "./src/main.js",
        ... ... ...
  plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "src"),
    }),
  ],
  mode: "development",
};
```

#### 1.5 运行指令

```bash
npx webpack
```

#### 1.6 .eslintignore配置

```bash
# 忽略dist目录下所有文件
dist
```

## 2.Babel

将 ES6 语法编写的代码转换为向后兼容的JavaScript语法。
配置文件有很多种写法

#### 2.1 下载包

```bash
npm i babel-loader @babel/core @babel/preset-env -D
```

#### 2.2 presets 预设  

就是一组Babel插件, 扩展Babel功能  

**@babel/preset-env**: 一个智能预设，允许您使用最新的 JavaScript。  
**@babel/preset-react**：一个用来编译 React jsx 语法的预设  
**@babel/preset-typescript**：一个用来编译 TypeScript 语法的预设  

babel.config.js  

```js
module.exports = {
  presets: ["@babel/preset-env"],
};
```

#### 2.3 webpack配置

webpack.config.js

```js
const path = require("path");
const ESLintWebpackPlugin = require("eslint-webpack-plugin");

module.exports = {
  module: {
    rules: [
          ... ... ...
      {
        test: /\.js$/,
        exclude: /node_modules/, // 排除node_modules代码不编译
        loader: "babel-loader",
      },
    ],
  },
          ... ... ...
  mode: "development",
};
```

#### 2.4 运行指令

```bash
npx webpack
```

打开打包后的 dist/static/js/main.js 文件查看，会发现箭头函数等 ES6 语法已经转换

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
