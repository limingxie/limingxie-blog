---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_全局事件总线(24)"
date: 2022-06-22T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

在 vue 里我们可以通过全局事件总线来实现任意组件之间通信  <!--more-->

它的原理是给 Vue 的原型对象上面添加一个属性。  
这样的话我所有组件的都可以访问到这个属性，然后可以通过这个属性来访问其他组件给这个属性上面绑定的一些方法，从而去传递数据。  
而且这个属性还可以去访问 Vue 实例对象上的方法。因为 Vue 组件构造函数的原型对象的原型对象是指向 Vue 的原型对象的  

main.js中，增加以下代码

```js
//引入Vue
import Vue from 'vue';
//引入App
import App from './App';

//关闭vue的生产提示
Vue.config.productionTip = false

//创建vm
new Vue({
    el: "#app",
    render: h => h(App),
    beforeCreate() {
        Vue.prototype.$bus = this//安装全局事件总线
    }
})
```

School.vue 中给 $bus 绑定 hello 事件

```js
<template>
  <div class="school">
    <h2>学校名称：{{ name }}</h2>
    <h2>学校地址：{{ address }}</h2>
  </div>
</template>

<script>
export default {
  name: "School",
  data() {
    return {
      name: "三里屯小学",
      address: "北京"
    }
  },
  mounted() {
    //给$bus绑定hello事件
    this.$bus.$on("hello", (data) => {
      console.log("我是school组件，到了数据：" + data);
    })
  },
  beforeDestroy() {
    this.$bus.off("hello")
  }
}
</script>

<style scoped>

</style>
```

Student.vue 中触发事件

```js
<template>
  <div class="student">
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生年龄：{{ age }}</h2>
    <button @click="sendStudentName">点我向School组件传递学生姓名</button>
  </div>
</template>

<script>
export default {
  name: "Student",
  data() {
    return {
      name: "张三",
      age: 18
    }
  },
  methods:{
    sendStudentName(){
      this.$bus.$emit("hello",this.name)
    }
  }
}
</script>

<style scoped>

</style>
```

## 总结

#### 全局事件总线(GlobalEventBus)

1.一种组件问通信的方式，适用于任意组件问通信  
2.安装全局事件总线：  

```js
new Vue({
    ......
    beforeCreate() {
        Vue.prototype.$bus = this//安装全局事件总线,$bus就相当于当前vm
    },
    ......
})
```

3.使用事件总线：  
①.接收数据：A组件想接收数据，则在A组件中给 $bus 绑定自定义事件，事件的​​​回调留在A组件自身​​  

```js
methods(){
    demo(data){......}
}
......
mounted() {
    //给$bus绑定hello事件
    this.$bus.$on("xxx",this.demo)
}
```

或者不写上边的 method，直接写成一个箭头函数  

②.提供数据：​​this.$bus.$emit( " xxx",数据)​​  

4.最好在​​beforeDestroy​​​钩子中，用​​$off​​去解绑当前组件所用到的事件  

#### todolist 改为全局事件总线

当我们删除和勾选一个任务时，实际上操作的是 Item 组件。  
通过 Item 组件操作 App.vue 中的数据。我们之前的写法是，在App.vue中先把删除和勾选的方法传给 List 组件，然后 List 传给了 Item。  
实际上 List 并没有用这些方法，只是进行了传递。所以这里适合用全局事件总线  

修改 main.js

```js
//引入Vue
import Vue from 'vue';
//引入App
import App from './App';

//关闭vue的生产提示
Vue.config.productionTip = false

//创建vm
new Vue({
    el: "#app",
    render: h => h(App),
    beforeCreate() {
        Vue.prototype.$bus = this
    }
})
```

App.vue 中不给 List 传东西了，同时 绑定事件

```js
<List :todos="todos"/>

<script>
......
export default {
  ......
  mounted() {
    this.$bus.$on("checkTodo",this.checkTodo)
    this.$bus.$on("deleteTodo",this.deleteTodo)
  },
  destroyed() {
    this.$bus.$off("checkTodo")
    this.$bus.$off("deleteTodo")
  }
}
</script>
......
```

相应的 List 中也不用接收了，也不需要给 Item 传了

```js
<template>
  <div>
    <ul class="todo-main">
      <Item v-for="todoObj in todos"
            :key="todoObj.id"
            :todo="todoObj"
      />
    </ul>
  </div>
</template>

<script>
import Item from "./Item"

export default {
  name: "List",
  props:["todos"],
  components: {
    Item
  }
}
</script>

```

Item 中也不需要接收了，触发方式也需要修改

```js
export default {
  name: "Item",
  props:['todo'],
  methods:{
    //勾选 or 取消勾选
    handleCheck(id){
      //this.checkTodo(id)
      this.$bus.$emit("checkTodo",id)
    },
    //删除
    handleDelete(id){
      if(confirm("确认删除吗?")){
        //this.deleteTodo(id)
        this.$bus.$emit("deleteTodo",id)
      }
    }
  }
}
```

#### $nextTick

App.vue 中增加样式

```js
.btn-edit {
  color: #ffffff;
  background-color: skyblue;
  border: 1px solid skyblue;
  margin-right: 10px;
}
```

同时增加修改 todo 的方法，同时绑定

```js
//更新todo
    updateTodo(id,title) {
      this.todos.forEach((todo) => {
        if (todo.id === id) todo.title = title
      })
    }

mounted() {
    ......
    this.$bus.$on("updateTodo",this.updateTodo)
  },
  destroyed() {
    ......
    this.$bus.$off("updateTodo")
  }
```

Item.vue 中增加编辑按钮，使用增加的样式  
增加点击事件 ​​handleEdit​​​，使用​​isEdit​​来判断是否处于编辑状态。这个方法中先判断有没有 isEdit 属性，如果没有则追加，如果有则直接改为 true  
增加 input ，值和 span 中显示的值一样，如果是编辑状态则展示。而 span 则相反  
同时 input 增加失去焦点事件 handleBlur，当失去焦点时，修改 todo 的值  

```js
<template>
  <li>
    <label>
      <input type="checkbox" :checked="todo.done" @change="handleCheck(todo.id)">
      <span v-show="!todo.isEdit">{{todo.title}}</span>
      <input type="text"
             v-show="todo.isEdit"
             :value="todo.title"
              @blur="handleBlur(todo,$event)"
              ref="inputTitle">
    </label>
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
    <button v-show="!todo.isEdit" class="btn btn-edit" @click="handleEdit(todo)">编辑</button>
  </li>

</template>

<script>
import pubsub from "pubsub-js"
export default {
  ......
    //编辑
    handleEdit(todo){
      if(Object.prototype.hasOwnProperty.call(todo, 'isEdit')){
        todo.isEdit = true;
      }else{
        this.$set(todo,"isEdit",true)
      }
      //同时获取焦点，
      this.$nextTick(function () {
        this.$refs.inputTitle.focus()
      })
    },
    //失去焦点处理
    handleBlur(todo,e){
      todo.isEdit = false
      if(!e.target.value.trim()) return alert("输入内容不能为空")
      this.$bus.$emit("updateTodo",todo.id,e.target.value)
    }
  }
}
</script>
```

nextTick
1.语法​​this.$nextTick(回调函数)  
2.作用：在下一次DOM更新结束后执行其指定的回调  
3.什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在 nextTick 所指定的回调函数中执行  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
