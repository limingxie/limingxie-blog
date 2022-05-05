---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介"
date: 2022-05-04T09:05:49+08:00
tags: [
    "Nodejs",
    "node",
    "npm",
]
categories: [
    "Nodejs",
]
---

公司项目用到了vue和nodejs...ㅠㅠ  
又要学新的开发语言了...^^;;

今天看着资料简单的整理了概念，然后搭建了一个http服务。  
如何去安装和配置开发环境，百度上一大把，这里就不介绍了。  

## 1.nodejs

先看看nodejs在[维基百科](https://zh.wikipedia.org/wiki/Node.js)里是怎么解释的。  

> Node.js 是能够在服务器端运行 JavaScript 的开放源代码、跨平台执行环境。

Node.js 大部分基本模块都用 JavaScript 语言编写。在 Node.js 出现之前，JavaScript 通常作为客户端程序设计语言使用，  
以JavaScript 写出的程序常在用户的浏览器上执行。  
Node.js 的出现使 JavaScript 也能用于服务端编程。  
Node.js 含有一系列内置模块，使得程序可以脱离 Apache HTTP Server 或 IIS，作为独立服务器执行。  

## 2.npm(Node Package Manager)

刚接接触nodejs，可能会对npm和nodejs的关系可能会有些困惑。  
npm就是nodejs的包管理器，因为nodejs的核心代码很少，想要处理一些复杂的功能就需要引用第三方引用包。  
这个第三方引用包就是npm帮nodejs做管理。  

> npm是Node.js默认的、用JavaScript编写的软件包管理系统。  

nodejs官网: <https://nodejs.org/zh-cn/>  
npm官网: <https://npmjs.org/>  

## 3.非阻塞处理  

Nodejs还有个重要概念，就是非阻塞处理。  
Nodejs非阻塞处理，引入了事件驱动模型。

什么是非阻塞处理的事件驱动模型？  
一般web服务，接受一个请求，它会线程池里需要找一个空闲的线程，或新开一个线程，执行请求。  
等请求处理完以后，它会释放这个线程。

Nodejs是服务器运行时，开一个事件循环(event-loop), 接受请求会放入到处理队列中，等待处理。  
如果有新的请求继续放到处理队列中，等处理完请求后，调用回调函数告诉请求已处理完毕。  
这种方式，可以让服务器更通畅的处理请求，减少拥塞带来的性能问题。  

看下面的例子：  

```Nodejs
console.log("-------------阻塞-----------");

function updateData1() {
    var s = new Date().getTime();
    while (new Date().getTime() < s + 3000);
};

updateData1();
console.log("updateData1 done");
console.log("updateData1 process done");

console.log("-------------非阻塞-----------");

function updateData2(done){
    setTimeout(() => {
        done();
    }, 3000);
}

updateData2(function (){ //调用updateData2的时候指定一个回调函数，调用完后执行这个回调函数。
    console.log("updateData2 done");
})
console.log("updateData2 process done");
```

看一下执行结果：  

```
$ node test.js   
-------------阻塞-----------
updateData1 done
updateData1 process done
-------------非阻塞-----------
updateData2 process done
updateData2 done
```

## 2. http服务搭建

简单的写一个web服务:  

config.js(配置信息) =>  

```Nodejs
const config = {
    hostname: '127.0.0.1',
    port: 3030
}

exports.config = config;
```

httpsample.js =>

```Nodejs
console.log("------------http test------------");

const http =require('http');
const config = require('./config.js').config; // 引用config文件

const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    switch (req.url) {
        case '/':
            res.end('Hello Man\n');
            break;
        case '/home':
            res.end('Hello Home\n');
            break;
        case '/hello':
            res.end('Hello World\n');
            break;
        default:
            res.end('invalid url\n');
            break;
    }
});

server.listen(config.port, config.hostname, () => {
console.log(`server running at http://${config.hostname}:${config.port}/`);
} );
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
