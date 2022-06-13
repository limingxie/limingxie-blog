---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_插件(20)"
date: 2022-06-18T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了组件相关的内容。  

## 插件

功能：用于增强 Vue
本质：包含 install 方法的一个对象，install 的第一个参数是 Vue，第二个以后的参数是插件使用者传递的数据
定义插件：

```js
对象.install = function(Vue,options){
  //1.添加全局过滤器
  vue.filter(...)
  //2.添加全局指令
  Vue.directive(...)
  //3.配置全局混入(合)
  Vue.mixin(...)
  //4.添加实例方法
  Vue.prototype.$myMethod = function(){}
  Vue.prototype.$myProperty = xxx
}

```

使用插件：​​Vue.use()​​  
我们着手写一个插件，跟 main.js 同级，新增一个 plugins.js  

```js
//完整写法
/*
const obj = {
    install(){
        console.log("install");
    }
}

export default obj*/
//简写
export default {
    install(Vue,x,y) {
        console.log(x,y)
        //全局过滤器
        Vue.filter('mySlice', function (value) {
            return value.slice(0, 4)
        })
        //定义全局指令
        Vue.directive('fbind', {
            bind(element, binding) {
                element.value = binding.value
            },
            inserted(element, binding) {
                element.focus()
            },
            update(element, binding) {
                element.value = binding.value
            }
        })
        //定义混入
        Vue.mixin({
            data() {
                return {
                    x: 100,
                    y: 200
                }
            }
        })
        //给Vue原型上添加一个方法(vm和vc就都能用了)
        Vue.prototype.hello = ()=>{alert("hello")}
    }
}
```

然后在 main.js 中使用插件

```js
//引入Vue
import Vue from 'vue';
//引入App
import App from './App';
//引入插件
import plugins from "@/plugins";
//关闭vue的生产提示
Vue.config.productionTip = false
//使用插件
//Vue.use(plugins)
//使用插件 并传参数
Vue.use(plugins,1,2)
//创建vm
new Vue({
    el: "#app",
    render: h => h(App)
})
```

在 Student.vue 中测试

```js
<template>
  <div>
    <h2>学生姓名：{{ name|mySlice }}</h2>
    <h2>学生性别：{{ sex }}</h2>
    <input type="text" v-fbind:value="name"><br><br>
    <button @click="test">点我测试 hello 方法</button>
  </div>
</template>

<script>
export default {
  name: "Student",
  data() {
    return {
      name: "张三12345",
      sex: "男",
    }
  },
  methods: {
    test() {
      this.hello()
    }
  }
}
</script>

//scoped 是这个样式之应用到这个组件的局部样式
<style scoped>
</style>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
