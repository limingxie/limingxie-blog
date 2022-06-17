---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_脚手架(18)"
date: 2022-06-16T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

## 1.安装

​ ​Vue CLI 官方网站​​ <https://cli.vuejs.org/zh/>  

CLI  
C：command  
L：line  
I：interface  

前提：Node.js安装  <!--more-->

1、执行​​npm install -g @vue/cli​​安装脚手架  

```bash
npm install -g @vue/cli
```

2、安装完成后，可以使用​​vue --version​​  

```bash
$ vue --version
@vue/cli 5.0.4
```

3、我们测试下，在桌面创建一个项目，先执行 ​​​cd Desktop​​​ 进入桌面，然后执行 ​​vue create hello-world​​  

然后选择第二个，因为我们目前学习的是 vue2  

```bash
Vue CLI v5.0.4
? Please pick a preset:
  Default ([Vue 3] babel, eslint)
❯ Default ([Vue 2] babel, eslint)
  Manually select features
```

当出现以下内容说明创建成功

```bash
 cd hello-world​​
 npm run serve
```

我们分别执行刚才两个命令，成功后会出现

```bash
 DONE  Compiled successfully in 4108ms 11:01:56 PM

  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.2.8:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

我们在浏览器打开 ​​​<http://localhost:8080​​​即可打开这个项目。而下面的​​http://192.168.2.8:8080/>​​​是同局域网的其他人可以访问的

## 2.分析脚手架结构

我们分析以下 main.js

```html
/**
* 该文件是整个文件的入口文件
*/
//引入vue
import Vue from 'vue'
//引入App组件，它是所有组件的父组件
import App from './App.vue'
//关闭vue的生产提示
Vue.config.productionTip = false
//创建vue实例对象，可以改为我们熟悉的写法
/*new Vue({
  //将App组件放入容器中（稍后解释）
  render: h => h(App),
}).$mount('#app')*/
new Vue({
    el: '#app',
    //将App组件放入容器中（稍后解释）
    render: h => h(App),
})
```

## 3.单文件组件  

命名注意  
一个单词：  
school.vue  
School.vue【推荐】  

多个单词：  
my-school.vue  
MySchool.vue【推荐】  

在 src–component 中创建 School.vue  

```html
<template>
  <!--组件的结构-->
  <div class="demo">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
    <button @click="showName">点我提示学校名</button>
  </div>
</template>

<script>
//组件交互相关的代码（数据、方法等）
export default {
  name: 'School',
  data() {
    return {
      name: "三里屯小学",
      address: "北京"
    }
  },
  methods: {
    showName() {
      alert(this.name)
    }
  }
}
//以上代码是下面代码的缩写
/*const school = Vue.extend({})
export default school*/
</script>

<style>
/*组件的样式*/
.demo {
  background-color: orange;
}
</style>
```

在 src–component 中创建 Student.vue

```html
<template>
  <!--组件的结构-->
  <div class="demo">
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
  </div>
</template>

<script>
//组件交互相关的代码（数据、方法等）
export default {
  name: 'Student',
  data() {
    return {
      name: "张三",
      age: 18
    }
  }
}
</script>
```

在 src 下修改 App.vue

```html
<template>
  <div>
    <School></School>
    <Student></Student>
  </div>
</template>

<script>
  //引入组件
  import Student from "./components/Student"
  import School from "./components/School"

  export default {
    name:'App',
    components:{
      School,
      Student
    }
  }
</script>

<style>

</style>
```

我们找到 src 中的 index.html，做下注释

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
      <!--针对IE浏览器的特殊配置，让IE浏览器以最高的渲染级别渲染页面-->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <!--开启移动端的理想视口-->
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
      <!--配置页签图标，BASE_URL 为 public 文件夹路径-->
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
      <!--配置网页标题 会找到 package.json 中的 name 做为标题-->
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <!--当浏览器不支持js时，noscript 标签中的元素就会显示-->
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <!--容器-->
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

## 4.render 函数  

关于不同版本的Vue  
1.vue.js 与 vue.runtime.xxx.js 的区别：  
(1).vue.js是完整版的 Vue，包含：核心功能+模板解析器  
(2).vue.runtime.xxx.js 是运行版的 Vue，只包含：核心功能；没有模板解析器  

2.因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用​​template​​​配置项，需要使用​​render​​​函数接收到的​​createElement​​函数去指定具体内容  

在 main.js 中  

```js
new Vue({
  //将App组件放入容器中（稍后解释）
  render: h => h(App),
    //render 完整写法是这样一个函数
    /*render(h){
        return h("h1","你好")
    }*/
}).$mount('#app')
```

## 5.修改默认配置  

vue.config.js 配置文件  
1、使用​​​vue inspect > output.js​​​可以查看到vue脚手架的默认配置，这条命令是把脚手架的配置输出到 output.js ，我们拉到最后可以找到入口文件的配置  
2、使用 vue.config.js 可以对脚手架进行个性化定制，详情见:  
​​ ​Vue CLI 配置参考​​​ 例如，我们上边修改的​ ​语法检查的配置项​​，我们还可以设置​ ​文件入口​​，我们把官网的代码复制过来，修改入口文件。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
