---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_消息订阅和发布(25)"
date: 2022-06-23T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇里整理了Vue 消息订阅与发布。  <!--more-->

1、首先安装 pubsubjs，执行 ​​npm i pubsub-js​​ pubsubjs 一个利用 JavaScript 进行发布/订阅的库  
2、由于是 School 需要接收订阅消息，所以在 School 中先引入 pubsubjs，然后订阅和取消订阅消息  

```js
<template>
  <!--组件的结构-->
  <div class="school">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
  </div>
</template>

<script>
import pubsub from "pubsub-js"

//组件交互相关的代码（数据、方法等）
export default {
  name: 'School',
  data() {
    return {
      name: "三里屯小学",
      address: "北京"
    }
  },
  mounted() {
    this.pubid = pubsub.subscribe("hello", (msgName, data) => {
      console.log("有人发布了hello消息，" + msgName + "回调执行了，收到数据：" + data);
    })
  },
  beforeDestroy() {
    pubsub.unsubscribe(this.pubid)
  }
}
</script>

<style>
/*组件的样式*/
.school {
  background-color: orange;
  padding: 10px;
}
</style>
```

需要注意，订阅消息时，回调函数写成箭头函数，或者放到 methods 中，否则方法中的 ​​this​​ 是 undefined  

3、Student 中引入 pubsubjs，并发布消息  

```js
<template>
  <!--组件的结构-->
  <div class="student">
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
    <button @click="sendStudentName">向School组件发送学生姓名</button>
  </div>
</template>

<script>
import pubsub from "pubsub-js"
//组件交互相关的代码（数据、方法等）
export default {
  name: 'Student',
  data() {
    return {
      name: "张三",
      age: 18
    }
  },
  methods: {
    sendStudentName() {
      pubsub.publish("hello",666)
    }
  }
}
</script>

<style>
/*组件的样式*/
.student {
  background-color: pink;
  padding: 10px;
  margin-top: 10px;
}
</style>
```

运行效果：

## 总结

消息订阅与发布(pubsub)  
1.一种组件间通信的方式，适用于任意组件间通信  
2.使用步骤:  
①安装pubsub: ​​​npm i pubsub-js​​​ ②引入:​​import pubsub from 'pubsub-js'​​
3.接收数据：  
A组件想接收数据，则在A组件中订阅消息，订阅的回调留在A组件自身  

```js
methods(){
  demo(data){......}
}
......
mounted() {
    this.pubid = pubsub.subscribe("xxx", this.demo)
  }
```

4.提供数据：​​pubsub.publish( "xxx” ,数据)​​​ 5.最好在​​beforeDestroy​​钩子中，用​​PubSub.unsubscribe(pid)​​去取消订阅  

todolist 使用消息订阅完成  
上一节中把消息勾选和删除改成了事件总线完成，现在我们把这里的删除改用消息订阅完成。由于 App.vue 中需要数据，即要删除的id，所以要订阅消息  

```js
......

<script>
//引入组件
//第三方库放前边
import pubsub from "pubsub-js"
import Header from "./components/Header";
......

export default {
  ......
  methods: {
    ......
    //删除todo，用下划线占位
    deleteTodo(_,id) {
      //过滤不改变原数组
      /*this.todos = this.todos.filter((todo)=>{
        return todo.id !== id
      })*/
      //可以精简为
      this.todos = this.todos.filter(todo => todo.id !== id)
    },
    ......
  },
  ......
  mounted() {
    this.$bus.$on("checkTodo",this.checkTodo)
    this.pubId = pubsub.subscribe("deleteTodo",this.deleteTodo)
  },
  destroyed() {
    this.$bus.$off("checkTodo")
    pubsub.unsubscribe(this.pubId)
  }
}
</script>
```

需要注意的是由于订阅消息的回调方法是 deleteTodo，这个方法是之前写好的，但需要修改，回调函数第一个参数是方法名称，  
所以我们需要增加一个参数，但由于我们没有用到这个消息名称参数，所以我们可以直接使用 ​​_​​下划线来占位  

Item 中需要发送消息  

```js
......

<script>
import pubsub from "pubsub-js"

export default {
  ......
    //删除
    handleDelete(id){
      if(confirm("确认删除吗?")){
        //this.deleteTodo(id)
        //this.$bus.$emit("deleteTodo",id)
        pubsub.publish("deleteTodo",id)
      }
    }
  }
}
</script>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
