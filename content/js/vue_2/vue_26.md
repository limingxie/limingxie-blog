---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_动画和过度效果(26)"
date: 2022-06-24T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇里整理了Vue 动画和过渡效果相关的内容。  <!--more-->

新建 Test.vue  

```js
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <transition>
      <h1 v-show="isShow">你好 Vue</h1>
    </transition>
  </div>
</template>

<script>
export default {
  name: "Test",
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style scoped>
h1 {
  background-color: orange;
}

.v-enter-active{
  animation: myAni 1s;
}

.v-leave-active{
  animation: myAni 1s reverse;
}

@keyframes myAni {
  from{
    transform: translateX(-100%);
  }
  to{
    transform: translateX(0px);
  }
}
</style>
```

App.vue

```js
<template>
  <div>
    <Test/>
  </div>
</template>

<script>
//引入组件
import Test from "./components/Test";

export default {
  name: 'App',
  components: {
    Test
  }
}
</script>

<style>
</style>
```

注意点
1、如果给 transition 标签增加了 name 属性

```js
<transition name="hello">
  <h1 v-show="isShow">你好 Vue</h1>
</transition>
```

那么动画过渡类名也需要修改：

```js
.hello-enter-active{
  animation: myAni 1s;
}

.hello-leave-active{
  animation: myAni 1s reverse;
}
```

2、如果想让程序一执行就执行一次动画，那么需要增加 appear  

注意 appear 前要有冒号，不写冒号就相当于一个普通属性，值是字符串是 “true”  

```js
<transition :appear="true">
      <h1 v-show="isShow">你好 Vue</h1>
    </transition>
```

或者简写

```js
<transition>
      <h1 v-show="isShow">你好 Vue</h1>
    </transition>
```

过渡效果  
单个元素  
复制一份 Test.vue 重命名为 Test2.vue，记得 App 中注册使用  

```js
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <transition appear>
      <h1 v-show="isShow">你好 Vue</h1>
    </transition>
  </div>
</template>

<script>
export default {
  name: "Test",
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style scoped>
h1 {
  background-color: orange;
  /*transition:  0.5s linear;*/
}

/*进入的起点，离开的终点*/
.v-enter, .v-leave-to {
  transform: translateX(-100%);
}
/*进入的过程，离开的过程*/
.v-enter-active, .v-leave-active {
  transition: 0.5s linear;
}
/*进入的终点，离开的起点*/
.v-enter-to, .v-leave {
  transform: translateX(0);
}
</style>
```

多个元素

```js
<transition-group>
      <h1 v-show="isShow" key="1">你好 Vue</h1>
      <h1 v-show="!isShow" key="2">闭关修炼 沉迷学习</h1>
    </transition-group>
```

Animate.css  
根据 ​ ​官网​​ 的使用教程安装、引入、使用即可  

1、复制一个 Test2.vue 重命名为 Test3.vue，并在 App 中引入使用  
2、安装 Animate.css，运行 ​​​npm install animate.css​​​执行安装  
3、Test3.vue 中引入，​​​import 'animate.css';​​​ 4、transition-group 标签中增加 ​​name="animate__animated animate__bounce"​​ 5、增加一个动画，例如我们增  加一个进入动画 ​​enter-active-class="animate__swing"​​，再增加一个离开动画  

其中官网右侧列出了动画名，点击可查看效果，同时后边可以复制动画名  

Test3.vue 完整代码  

```js
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <transition-group
        appear
        name="animate__animated animate__bounce"
        enter-active-class="animate__swing"
        leave-active-class="animate__backOutUp"
        >
      <h1 v-show="isShow" key="1">你好 Vue</h1>
      <h1 v-show="!isShow" key="2">闭关修炼 沉迷学习</h1>
    </transition-group>
  </div>
</template>

<script>
import 'animate.css';
export default {
  name: "Test",
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style scoped>
h1 {
  background-color: orange;
}
</style>
```

## 总结  

Vue 封装的过渡与动画  
1.作用：在插入、更新或移除DOM元素时，在合适的时候给元素添加样式类名  

摘自 ​ ​Vue 官网​​  

3.写法：  
1）准备好样式：  

元素进入的样式:  
1.​​​v-enter​​​进入的起点  
2.​​​v-enter-active​​​进入过程中  
3.​​​v-enter-to​​进入的终  
元素离开的样式：  
1.​​​v-leave​​​离开的起点  
2.​​​v-leave-active​​​离开过程中  
3.​​​v-leave-to​​离开的终点  
2）使用​​<transition>​​​包裹要过度的元素，并配置​​name​​​属性：​​<transition name="hello">​​(可选)  

3.备注：若有多个元素需要过度，则需要使用​​<transition-group>​​，且每个元读都要指定 key 值  

todolist 增加动画效果  
现在给增加和删除增加动画效果，所以修改 Item.vue 用 ​​<transition>​​标签包裹，然后增加动画即可  

```js
<template>
  <transition name="todo" appear>
  ......
    </li>
  </transition>
</template>

<script>
......
</script>

<style scoped>
......

.todo-enter-active {
  animation: myAni 1s;
}

.todo-leave-active {
  animation: myAni 1s reverse;
}

@keyframes myAni {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0px);
  }
}
</style>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
