---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_编程式路由导航(35)"
date: 2022-07-03T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了路由相关的内容。  <!--more-->

## router- link的replace属性  

1.作用：控制路由跳转时操作浏览器历史记录的模式  
2.浏览器的历史记录有两种写入方式：分别为​​​push​​​和​​replace​​​，​​push​​​是追加历史记录，​​replace​​​是替换当前记录。路由跳转时候默认为​​push​​​ 3.如何开启​​replace​​模式​​<router-link replace .......>News</router-link>  ​​

## 编程式路由导航  

作用：不借助​​<router-link>​​​ 实现路由跳转，让路由跳转更加灵活  
具体编码：  

```js
/ /$router的两个API
this.$router.push({
  name : 'xiangqing',
  params :{
    id :xxx,
    title :xXX
  }
})
this.$router.replace({
  name : "xiangqing',
  params :{
    id : xxX,
    title :xXX
  }
})
this.$router.forward()//前进
this.$router.back()//后退
this.$router.go()//可前进也可后退
```

修改 Banner

```js
<template>
  <div class="col-xs-offset-2 col-xs-8">
    <div class="page-header">
      <h2>Vue Router Demo</h2>
      <button @click="back">后退</button>
      <button @click="forward">前进</button>
      <button @click="test">测试一下 go</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Banner",
  methods:{
    back(){
      this.$router.back()
    },
    forward(){
      this.$router.forward()
    },
    test(){
      this.$router.go(-3)
    }
  }
}
</script>
```

修改 Message

```js
<!--跳转路由并携带params参数，to的对象写法-->
        ......
        <button @click="pushShow(m)">push查看</button>
        <button @click="replaceShow(m)">replace查看</button>

<script>
export default {
  ......
  methods: {
    pushShow(m) {
      this.$router.push({
        name: 'xiangqing',
        query: {
          id: m.id,
          title: m.title
        }
      })
    },
    replaceShow(m) {
      this.$router.replace({
        name: 'xiangqing',
        query: {
          id: m.id,
          title: m.title
        }
      })
    }
  }
}
</script>
```

## 缓存路由组件

作用：让不展示的路由组件保持挂载，不被销毁  
具体编码：  

```js
<keep-alive include="News">
  <router-view></router-view>
</keep-alive>
```

修改 News 组件，每个消息后展示输入框

```js
<template>
  <ul>
    <li>news001 <input/></li>
    <li>news002 <input/></li>
    <li>news003 <input/></li>
  </ul>
</template>
```

当我们在后边输入框中输入文字，切换到别的链接，再切回来，会发现 input 内的文字就清空了，因为我们之前说过，当 News 切换走的时候，它已经被销毁了，我们可以在 ​​beforeDestroy​​​ 中打印测试，这里不做演示

想要缓存这个页面，可以在 News 的最终展示区 Home 中增加标签​​​router-view​​​，然后使用 ​​include​​​ 标识要缓存的​​组件名​​

```js
<keep-alive include="News">
  <router-view></router-view>
</keep-alive>
```

注意：  
1、​​​include​​​后是组件名称  
2、不写会缓存所有的  
3、如果想缓存多个，可以写成数组  

```js
:include="['News','Message']"
```

## 两个新的生命钩子

作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态  
​​​activated​​​路由组件被激活时触发  
​​​deactivated​​路由组件失活时触发  

现在的需求是，我们希望在 News 组件周而复始的展示一个文字，之前我们写过就是使用 ​​setInterval​​，然后在 beforeDestroy 中再销毁这个 setInterval，但是由于我们上一节中，已经把 News 设置了缓存，不会走 beforeDestroy 了，所以我们引入两个新的生命周期钩子  

修改 News  

```js
<template>
  <ul>
    <li :style="{opacity}">欢迎学习Vue</li>
    <li>news001 <input/></li>
    <li>news002 <input/></li>
    <li>news003 <input/></li>
  </ul>
</template>

<script>
export default {
  name: "News",
  data() {
    return {
      opacity: 1
    }
  },
  activated() {
    this.timer = setInterval(() => {
      this.opacity -= 0.01
      if (this.opacity <= 0) this.opacity = 1
    },16)
  },
  deactivated() {
    clearInterval(this.timer);
  }
}
</script>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
