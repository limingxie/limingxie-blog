---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_配置代理(27)"
date: 2022-06-25T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇里整理了Vue 动画和过渡效果相关的内容。  

## 方式一  

首先安装 axios：​​npm i axios​​  

修改 App.vue  

```js
<template>
  <div>
    <button @click="getBookInfo">获取书籍信息</button>
  </div>
</template>

<script>
import axios from "axios"
export default {
  name: 'App',
  methods:{
    getBookInfo(){
      axios.get("http://localhost:8080/downloads/data.json").then(
          response =>{
            console.log("请求成功了",response.data);
          },
          error => {"请求失败了",error.message}
      )
    }
  }
}
</script>
```

点击按钮请求书籍的信息，其中​​<http://localhost:8080/downloads/data.json>​​​返回了书籍信息：  

当我们点击按钮时，却发现报错了：  

这是跨域请求的错误，当协议名（http）、主机名（localhost）、端口号（8080） 中有一个不一致会提示跨域请求。  
解决办法有：1、cors；2、jsonp；3、代理服务器  
我们来学习第三个，可以先查看下官方文档 ​ ​Vue Cli 配置参考：devServer.proxy​​  
流程：8081 向 8080 请求数据，我们开启一个代理服务器，端口号和我们的 8081 相同  
  
修改  
1、 ​​​vue.config.js​​，直接复制官网代码，修改端口号为请求数据的端口8080  

```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  lintOnSave:false,
  //开启代理服务器
  devServer: {
    proxy: 'http://localhost:8080'
  }
})
```

2、请求数据端口修改为本地端口 8081  
3、由于修改了脚手架配置，所以先停掉端口，然后重新运行  

可以看到请求成功了  

## 方式二  

方式一有两个不完美的地方：  
1、只能配置一个代理服务器。  
2、当本地也就是 public 中有相同数据，就不请求代理服务器了  

1、修改 vue.config.js，注释掉方式一的代码，改为  

```js
devServer: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        pathRewrite:{'^/api':''},
        ws: true,
        changeOrigin: true
      }
    }
  }
```

其中​​/api​​​为请求前缀，可以改成其他名字  
​​​pathRewrite​​​为路径重写，匹配api开头的字符串，并把api替换为空字符串，这样才能保证代理服务器转发给 8080 的不带前边的前缀  
​​​ws​​​ 用于支持websocket  
​​​changeOrigin​​请求来自于。即控制请求头中host数据。默认为true说谎:来自8080;false如实回答来自代理服务器8081  

2、请求路径增加前缀  
3、由于修改了脚手架配置，所以先停掉端口，然后重新运行  

## vue 脚手架配置代理总结  

方法一  
在​​​vue.config.js​​中添加如下配置：  

```js
devServer: {
    proxy: 'http://localhost:8080'
}
```

说明：
1.优点：配置简单，请求资源时直接发给前端(8080)即可。  
2.缺点：不能配置多个代理，不能灵活的控制请求是否走代理  
3.工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器（优先匹配前端资源)  

方法二

```js
devServer: {
    proxy: {
      '/api': {//匹配所有以/api开头的路径
        target: 'http://localhost:8080',//代理目标的基础路径
        pathRewrite:{'^/api':''},//路径重写，匹配api开头的字符串，并把api替换为空字符串
        ws: true,//用于支持websocket
        changeOrigin: true//请求来自于。即控制请求头中host数据。默认为true说谎:来自8080;false如实回答来自代理服务器8081
      }
    }
  }
```

说明：  
1.优点：可以配置多个代理，且可以灵活的控制请求是否走代理。  
2.缺点：配置略微繁琐，请求资源时必须加前缀。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
