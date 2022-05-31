---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_express"
date: 2022-05-28T09:05:49+08:00
tags: [
    "Nodejs",
    "express",

]
categories: [
    "Nodejs",
]
---

这篇使用Express框架搭建一个简单的web服务器。  

## 1.初始化和安装Express框架

```
npm init -y
npm install express --save
npm install nodemon --save-dev   //开发模式有变化自动编译
npm install body-parser --save   //读取请求body值
npm install cors --save

```

## 2.修改package.json

然后都是在目录下创建一个index.js文件。
在package.json文件中找【scripts】设置添加 { "dev": "nodemon app.js" }

packgae.json =>  
"scripts": {  
    "dev": "nodemon app.js"  
}  

## 3.实例代码

```js
'use strict';
const express = require('express');
const bodyParser = require('body-parser');
const http = require('http');
const { nextTick } = require('process');

//中间件
const printLog = (req, res, next)=>{
    console.log("middileware.log =>", req.method, req.url);
    next();
}

const app = express();
app.use(bodyParser.urlencoded({
    extended: true
}));
app.use(bodyParser.json());
app.use(printLog);

//默认
app.get("/", (req, res) => {
    res.send("server is working");
});

//重定向
app.get("/blog", (req, res) => {
    res.redirect("/");
});

//json返回值
app.get("/json", (req, res) => {
    res.json({
        result: 'OK'
    })
});

//返回http状态码
app.get("/sitemap", (req, res) => {
    res.redirect(301, "/");
});

//http状态码 404
app.get("/adult", (req, res) => {
    res.status(404).end();
});

//post方式
app.post('/login', (req, res) => {
    console.log(req.body);
    res.json({
        post_result: 'OK',
        body: req.body,
    });
});


//post方式
app.get('/info/:id', (req, res) => {
    res.json({
        //HTTP方法
        methods: http.METHODS,
        //http状态码
        status_codes: http.STATUS_CODES,
        //请求头
        headers: req.headers,
        //主机名
        hostname: req.hostname,
        //请求url
        url: req.url,
        ip: req.ip,
        method: req.method,
        //请求协议，http,https
        protocol: req.protocol,
        //域名数组
        subdomains: req.subdomains,
        //请求串
        query: req.query,
        //请求参数
        params: req.params,
        //请求体
        body: req.body
    });
});

/*
{
    "methods": [
       ... ...
    ],
    "status_codes": {
        ... ...
    },
    "headers": {
        ... ...
    },
    "hostname": "127.0.0.1",
    "url": "/info/1?appid=2",
    "ip": "::ffff:127.0.0.1",
    "method": "GET",
    "protocol": "http",
    "subdomains": [],
    "query": {
        "appid": "2"
    },
    "params": {
        "id": "1"
    },
    "body": {
        "userNmae": "xiaoming",
        "passWord": "1111"
    }
}
*/


const auth = (req, res, next)=>{
    console.log("middileware.log =>", req.method, req.url, req.query);
    if(req.query.uid == 'admin'){
        next();
    } else {
        res.status(403).end();
    }
}

//auth验证
app.get("/admin", auth, (req, res) => {
    res.send('管理员');
});

//cors设定
const cors = require('cors');
app.use(cors());

app.get('/search', (req, res) => {
    res.send("search")
})

// var corsOptions = {
//     origin: 'https://xxx.com',
//     optionsSuccessStatus:200
// }
// app.use(cors(corsOptions));

const PORT = process.env.PORT || 3000
app.listen(PORT, (err) => {
    if (err) {
        console.error("出错了：", err);
    }
    console.log("服务启动:", "http://127.0.0.1:" + PORT)
})
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
