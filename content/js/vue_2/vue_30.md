---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_Vuex(30)"
date: 2022-06-28T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了Vuex相关的内容。  <!--more-->

## 一、理解Vuex

#### 1.1、Vuex是什么

专门在vue中实现集中式状态(数据）管理的一个Vue插件，对Vue应用中多个组件的共享状态进行集中式的管理(读/写)，也是一种组件间通信的方式，且适用于任意组件间通信

#### 1.2、什么时候使用Vuex

1. 多个组件依赖于同—状态
2. 来自不同组件的行为需要变更同—状态

#### 1.3、Vuex工作原理图

## 二、求和案例

#### 2.1、使用vue编写

src/App.vue文件

```js
<template>
  <div>
    <Count/>
  </div>
</template>

<script>
import Count from "./components/Count.vue";

export default {
  name: "App",
  components: { Count },
};
</script>
```

src/components/App.vue文件

```js
<template>
  <div>
    <h2>当前求和为：{{ sum }}</h2>
    <select v-model.number="n">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="incrementOdd">当前求和为奇数再加</button>
    <button @click="incrementWait">等一等再加</button>
  </div>
</template>

<script>
  export default {
    name: "Count",
    data() {
      return {
        sum: 0, // 当前的和
        n: 1, // 用户选择的数字
      };
    },
    methods: {
      increment() {
        this.sum += this.n;
      },
      decrement() {
        this.sum -= this.n;
      },
      incrementOdd() {
        if (this.sum % 2) {
          this.sum += this.n;
        }
      },
      incrementWait() {
        setTimeout(() => {
          this.sum += this.n;
        }, 500);
      },
    },
  };
</script>

<style>
  button {margin-left: 5px;}
</style>
```

#### 2.2、搭建Vuex环境

1、下载安装vuex npm i vuex
2、创建src/store/index.js该文件用于创建Vuex中最为核心的store

```js
import Vue from 'vue'
import Vuex from 'vuex' // 引入Vuex

Vue.use(Vuex) // 应用Vuex插件

const actions = {}  // 准备actions——用于响应组件中的动作
const mutations = {} // 准备mutations——用于操作数据（state）
const state = {}   // 准备state——用于存储数据

// 创建并暴露store
export default new Vuex.Store({
 actions,
 mutations,
 state,
})
```

3、在src/main.js中创建vm时传入store配置项

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store' // 引入store

Vue.config.productionTip = false

new Vue({
 el: '#app',
 render: h => h(App),
 store,          // 配置项添加store
 beforeCreate() {
  Vue.prototype.$bus = this
 }
})
```

#### 2.3、使用Vuex编写  

**Vuex的基本使用**  

1．初始化数据state，配置actions 、mutations，操作文件store.js  
2．组件中读取vuex中的数据$store.state.数据  
3．组件中修改vuex中的数据$store.dispatch('action中的方法名',数据)或$store.commit( ' mutations中的方法名",数据)若没有网络请求或其他业务逻辑，组件中也可越过  actions，即不写dispatch，直接编写commit  

**src/store/index.js该文件用于创建Vuex中最为核心的store**

```js
import Vue from 'vue'
import Vuex from 'vuex' // 引入Vuex

Vue.use(Vuex) // 应用Vuex插件

// 准备actions——用于响应组件中的动作
const actions = {
 /* jia(context,value){
  console.log('actions中的jia被调用了')
  context.commit('JIA',value)
 },
 jian(context,value){
  console.log('actions中的jian被调用了')
  context.commit('JIAN',value)
 }, */
 jiaOdd(context,value){ // context 相当于精简版的 $store
  console.log('actions中的jiaOdd被调用了')
  if(context.state.sum % 2){
   context.commit('JIA',value)
  }
 },
 jiaWait(context,value){
  console.log('actions中的jiaWait被调用了')
  setTimeout(()=>{
   context.commit('JIA',value)
  },500)
 }
}
// 准备mutations——用于操作数据（state）
const mutations = {
 JIA(state,value){
  console.log('mutations中的JIA被调用了')
  state.sum += value
 },
 JIAN(state,value){
  console.log('mutations中的JIAN被调用了')
  state.sum -= value
 }
}
// 准备state——用于存储数据
const state = {
 sum:0 //当前的和
}

// 创建并暴露store
export default new Vuex.Store({
 actions,
 mutations,
 state,
})
```

**src/components/Count.js该文件用于创建Vuex中最为核心的store**

```js
<template>
 <div>
  <h1>当前求和为：{{ $store.state.sum }}</h1>
  <select v-model.number="n">
   <option value="1">1</option>
   <option value="2">2</option>
   <option value="3">3</option>
  </select>
  <button @click="increment">+</button>
  <button @click="decrement">-</button>
  <button @click="incrementOdd">当前求和为奇数再加</button>
  <button @click="incrementWait">等一等再加</button>
 </div>
</template>

<script>
 export default {
  name:'Count',
  data() {
   return {
    n:1, //用户选择的数字
   }
  },
  methods: {
   increment(){
    this.$store.commit('JIA',this.n)
   },
   decrement(){
    this.$store.commit('JIAN',this.n)
   },
   incrementOdd(){
    this.$store.dispatch('jiaOdd',this.n)
   },
   incrementWait(){
    this.$store.dispatch('jiaWait',this.n)
   },
  }
 }
</script>

<style lang="css">button{margin-left: 5px;}</style>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
