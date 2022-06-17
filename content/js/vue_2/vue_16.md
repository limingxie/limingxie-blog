---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_生命周期(16)"
date: 2022-06-14T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了生命周期相关的内容。  <!--more-->

#### 生命周期

1.又名：生命周期回调函数、生命周期函数、生命周期钩子  
2.是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数  
3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的  
4.生命周期函数中的 this 指向是 vm 或组件实例对象  

做一个小例子，要求页面的文字一上来透明度就周而复始的从1变到0  

```html
<div id="app">
    <h2 :style="{opacity: opacity}">好好学习</h2>
    <h2 :style="{opacity}">天天向上</h2>
</div>
```

```html
<script type="text/javascript">
    Vue.config.productionTip = false
    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            opacity: 1
        },
        //Vue完成模板解析，并把初始的真实dom放入页面后(挂载完毕)调用mounted
        mounted(){
            setInterval(()=>{
                this.opacity -= 0.01
                if(this.opacity <=0) this.opacity = 1
            },20)
        }
    })
</script>
```

#### 生命周期流程

[图片备用地址](https://limingxie.github.io/images/js/vue/life_cycle.png)  
![vue_key_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/life_cycle.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue初识</title>
    <script type="text/javascript" src="./js/vue.js"></script>
</head>
<body>
<div id="app">
    <h2 :style="{opacity}">天天向上</h2>
    <button @click="opacity = 1">透明度设置为1</button>
    <button @click="stop">点我停止变换</button>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            opacity: 1
        },
        methods:{
          stop(){
              this.$destroy()
          }
        },
        mounted(){
            this.timer = setInterval(()=>{
                this.opacity -= 0.01
                if(this.opacity <=0) this.opacity = 1
            },20)
        },
        beforeDestroy(){
            console.log("beforeDestroy")
            clearInterval(this.timer)
        }
    })
</script>
</body>
</html>
```

#### 总结

vm的一生(vm的生命周期)  
将要创建------>调用​​​beforeCreate​​​函数  
创建完毕------>调用​​​created​​​豳数  
将要挂载------>调用​​​beforeMount​​​函数  
挂载完毕------>调用​​​mounted​​​函数【重要的钩子】  
将要更新------>调用​​​beforeUpdate​​​函数  
更新完毕------>调用​​​updated​​​函数。  
将要销毁------>调用​​​beforeDestroy​​​函数【重要的钩子】  
销毁完毕------>调用​​​destroyed​​函数  

**常用的生命周期钩子**  
1.​​​mounted​​​：发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】  
2.​​​beforeDestroy​​：清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】  

**关于销毁Vue实例**  
1、销毁后借助Vue开发者工具看不到任何信息  
2、销毁后自定义事件会失效，但原生DOM事件依然有效  
3、一般不会在​​​beforeDestroy​​操作数据，因为即便操作数据，也不会再触发更新流程了  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
