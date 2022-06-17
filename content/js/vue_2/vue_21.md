---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_小案例(21)"
date: 2022-06-19T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇做了一个小案例。  <!--more-->

App.vue

```js
<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <MyHeader :addtodo="addtodo"/>
        <MyList :todolist="todolist" :checktodo="checktodo" :deltodo="deltodo"/>
        <MyFoot :todolist="todolist" :checkalltodo="checkalltodo" :delAll="delAll"/>
      </div>
    </div>
  </div>
</template>

<script>
import MyHeader from "./components/myHeader";
import MyList from "./components/myList";
import MyFoot from "./components/myFoot";

export default {
  name: 'App',
  components: {
    MyHeader,
    MyList,
    MyFoot
  },
  data() {
    return {
      todolist:[
        {id:'001',title:'吃饭',done:false},
        {id:'002',title:'睡觉',done:true},
        {id:'003',title:'打游戏',done:false}
      ]
    }
  },
  methods:{
    addtodo(newtodo){
      this.todolist.unshift(newtodo)
    },
    checktodo(id){
      this.todolist.forEach( value => {
        if(value.id === id) value.done = !value.done
      })
    },
    checkalltodo(status){
      this.todolist.forEach( value => {
        value.done = status
      })
    },
    deltodo(id){
      this.todolist = this.todolist.filter((todo) => {
        return todo.id !== id
      })
    },
    delAll(){
      this.todolist = this.todolist.filter((todo) =>{
        return todo.done != true
      })
    }
  }
}
</script>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
```

myHeader.vue

```js
<template>
  <div class="todo-header">
    <input type="text" placeholder="请输入你的任务名称，按回车键确认" v-model="title" @keyup.enter="addNew"/>
  </div>
</template>
 
<script>
import {nanoid} from 'nanoid'
export default {
  name: "MyHeader",
  data(){
    return {
      title:''
    }
  },
  props:['addtodo'],
  methods:{
    addNew(){
      this.title = this.title.trim()
      if(!this.title) return alert('请输入内容')
      const newToObj = {id:nanoid(),title:this.title,done:false}
      this.title=''
      this.addtodo(newToObj)
    }
  }
}
</script>
 
<style scoped>
/*header*/
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>
```

myList.vue

```js
<template>
  <ul class="todo-main">
    <MyItem v-for="todoObj in todolist" :key="todoObj.id" :todoObj="todoObj" :checktodo="checktodo" :deltodo="deltodo"/>
  </ul>
</template>

<script>
import MyItem from "./myItem";
export default {
  name: "MyList",
  props:['todolist','checktodo','deltodo'],
  components:{
    MyItem
  }
}
</script>

<style scoped>
/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>
```

myItem.vue

```js
<template>
  <ul class="todo-main">
    <MyItem v-for="todoObj in todolist" :key="todoObj.id" :todoObj="todoObj" :checktodo="checktodo" :deltodo="deltodo"/>
  </ul>
</template>

<script>
import MyItem from "./myItem";
export default {
  name: "MyList",
  props:['todolist','checktodo','deltodo'],
  components:{
    MyItem
  }
}
</script>

<style scoped>
/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>
```

myFoot.vue

```js
<template>
  <div class="todo-footer">
    <label>
      <input type="checkbox" v-model="isAll" />
    </label>
    <span>
      <span>已完成{{doneTodo}}</span> / 全部{{todol}}
    </span>
    <button class="btn btn-danger" @click="delchecktodo">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: "MyFoot",
  props:['todolist','checkalltodo','delAll'],
  computed:{
    todol(){
      return this.todolist.length
    },
    doneTodo(){
      return this.todolist.reduce((pre,current)=> {
        return pre + (current.done ? 1 : 0)
      },0)
    },
    isAll:{
      set(value){
        return this.checkalltodo(value)
      },
      get(){
        return this.todol === this.doneTodo && this.todol != 0
      }
    }
  },
  methods:{
    delchecktodo(){
      if(confirm("确认删除嘛")){
        this.delAll()
      }
    }
  }
}
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>
```

## 总结TodoList案例

1.组件化编码流程：  
（1）拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突。  
（2）实现动态组件：考虑好数据的存放位置，数据是一个组件在用，害死一些组件在用：  

一个组件在用 放在组件自身即可  
一些组件在用 放在他们共同的父组件上**（状态提升）**  
（3）实现交互：从绑定事件开始。  
2.props适用于：  
（1）父组件==>子组件 通信  
（2）子组件==>父组件 通信（要求父先给子一个函数）  
3.试用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的！  
4.props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。  

<!-- 可以把数据存到localStorage，这时候要使用watch监视实现功能(deep：true); -->

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
