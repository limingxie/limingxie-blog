---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_vuex多组件(31)"
date: 2022-06-29T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

## getters 配置项

index.js 中增加 getters 配置项

```js
//准备getters，用于将state中的数据进行加工
const getters = {
    bigSum(state){
        return state.sum*10
    }
}

//创建并暴露store
export default new Vuex.Store({
    ......
    getters,
});
```

Count.vue 中使用

```html
<h1>当前求和10倍为：{{$store.getters.bigSum}}</h1>
```

#### 总结

1.概念：当 state 中的数据需要经过加工后再使用时，可以使用 getters 加工
2.在 ​​​store.js​​​中追加 ​​getters​​ 配置

```js
//准备getters，用于将state中的数据进行加工
const getters = {
    bigSum(state){
        return state.sum*10
    }
}

//创建并暴露store
export default new Vuex.Store({
    ......
    getters,
});
```

3.组件中读取数据：​​$store.getters.bigSum​​  

## mapState、mapGetters  

首先引入问题。我们在 index.js 中增加学校和学科字段  

```js
const state = {
    sum: 0,//当前和
    school:"三里屯小学",
    subject:"Vue",
}
```

Count.vue 中使用

```js
<h1>当前求和为：{{$store.state.sum}}</h1>
    <h3>当前求和10倍为：{{$store.getters.bigSum}}</h3>
    <h3>我在：{{$store.state.school}}学习{{$store.state.subject}}</h3>
```

我们发现每次取值时都是 ​​​store.state.xxx​​​或者​​$store.getters.xxx​​，太长了，有的同学想到了写计算属性来简化

```js
<h1>当前求和为：{{he}}</h1>
    <h3>当前求和10倍为：{{$store.getters.bigSum}}</h3>
    <h3>我在：{{xuexiao}}学习{{xueke}}</h3>

computed:{
    he(){
      return this.$store.state.sum
    },
    xuexiao(){
      return this.$store.state.school
    },
    xueke(){
      return this.$store.state.subject
    }
  }
```

当然可以使用要学习的这个 ​​mapState​​

```js
<h1>当前求和为：{{he}}</h1>
    <h3>当前求和10倍为：{{$store.getters.bigSum}}</h3>
    <h3>我在：{{xuexiao}}学习{{xueke}}</h3>
    computed:{
        //借助mapstate生成计算属性，从state中读取数据（对象写法）
        ...mapState({he:"sum",xuexiao:"school",xueke:"subject"})
        //或者
        //借助mapstate生成计算属性，从state中读取数据（数组写法）
        ...mapState(['sum','school',"subject"])
    },
```

其中…这里是 ES6 的语法，举个例子

```js
let obj1 = {x:100,y:200}
    let obj2 = {
            a:1,
            ...obj1,
            b:2,
     }
     console.log(obj2);
```

所以​​​...mapState({he:"sum",xuexiao:"school",xueke:"subject"})​​就相当于我们在 computed 中增加了开始写的那一堆方法  

同样 mapGetters  

```js
<h3>当前求和10倍为：{{ bigSum }}</h3>

computed: {
   ......
    //...mapGetters({bigSum:'bigSum'})
    ...mapGetters(['bigSum'])
  },
```

## mapActions、mapMutations  

mapMutations 对象写法  

```js
<button @click="increment(n)">+</button>
    <button @click="decrement(n)">-</button>

  methods: {
    /*increment() {
      this.$store.commit("JIA", this.n);
    },
    decrement() {
      this.$store.commit("JIAN", this.n);
    },*/
    ...mapMutations({"increment":"JIA","decrement":"JIAN"}),
   ......
  }

```

mapMutations 数组写法

```js
<button @click="JIA(n)">+</button>
    <button @click="JIAN(n)">-</button>

    //借助 mapMutations 生成对用的方法，方法中会调用 commit去联系mutations(数组写法)
    ...mapMutations(["JIA","JIAN"]),

```

数组的这种写法意思是生成方法名为 JIA，commit 的方法名也为 JIA 才能这样写，所以调用时，我们方法名要写 JIA，同样的也要把参数传进去

mapAtions 对象写法

```js
<button @click="incrementOdd(n)">当前和为奇数再加</button>
    <button @click="incrementWait(n)">等一等再加</button>

methods:{
    //借助 mapActions 生成对用的方法，方法中会调用 dispatch 去联系 actions(对象写法)
    ...mapActions({incrementOdd:"jiaOdd",incrementWait:"jiaWait"})
}
```

mapAtions 数组写法

```js
<button @click="jiaOdd(n)">当前和为奇数再加</button>
    <button @click="jiaWait(n)">等一等再加</button>
    methods:{
    //借助 mapActions 生成对用的方法，方法中会调用 dispatch 去联系 actions(数组写法)
    ...mapActions(["jiaOdd","jiaWait"])
    }

```

## 多组件共享数据

现在再写一个 Person 组件，展示人员信息。要完成 Person 组件展示刚才 Count 组件中的 sum 值。而 Count 组件展示人员信息  

我们首先完成 Person 组件的人员展示和添加。首先在 index.js 中的 state 中存入 ​​personList​​​ 做为要展示的人员数据。然后在 Person.vue 中使用 ​​v-for​​ 循环出人员数据  

然后实现添加人员方法。正常应该在 index.js 中的 actions 写方法，然后 commit 给 mutations，但是因为逻辑比较简单，所以我们直接在 mutations 中写一个添加人员的方法   ​​ADD_PERSON​​​，然后在 Person.vue 中使用 ​​this.$store.commit​​​提交添加的人员数据即可。先看效果：  

完整代码如下(仅展示改动的代码)：  

index.js  

```js
......
//准备mutations；用于操作数据（state）
const mutations = {
    ......
    ADD_PERSON(state,value){
        console.log("mutations中的ADD_PERSON被调用了",state,value);
        state.personList.unshift(value)
    }
}

//准备state；用于存储数据
const state = {
    ......
    personList:[
        {id:"001",name:"张三"},
        {id:"002",name:"李四"}
    ]
}
......
```

Person.vue

```js
<template>
  <div class="category">
    <h1>人员信息</h1>
    <input type="text" placeholder="请输入名字" v-model="name"/>
    <button @click="add">添加</button>
    <ul>
      <li v-for="person in personList" :key="person.id">{{person.name}}</li>
    </ul>
  </div>
</template>

<script>
import {nanoid} from "nanoid"
export default {
  name: "Person",
  data(){
    return{
      name:""
    }
  },
  methods:{
    add(){
      const personObj = {id:nanoid(),name:this.name}
      this.$store.commit("ADD_PERSON",personObj)
    }
  },
  computed:{
    personList(){
      return this.$store.state.personList;
    }
  }
}
</script>

<style scoped>
select, button {
  margin-right: 5px;
}
</style>
```

App.vue 中引入组件并使用

```js
<template>
  <div>
    <Count/>
    <hr>
    <Person/>
  </div>
</template>

<script>
import Count from "@/components/Count";
import Person from "@/components/Person";

export default {
  name: 'App',
  components: {Count,Person},
}
</script>

<style>

</style>
```

下面实现数据共享，我们让 Count 组件展示 Person 组件中总人数，Person 组件展示 Count 组件的求和数

修改 Count 组件

```js
<h3 style="color: red">Person组件的总人数为{{personList.length}}</h3>

<script>
......
export default {
  ......
  computed: {
  ...
    ...mapState(['sum','school',"subject","personList"])
    ...
  }
  ......
}
</script>
```

修改 Person 组件

```js
<h3 style="color: red">Count组件求和为{{sum}}</h3>
<script>
......
export default {
  ......
  computed:{
    ......
    sum(){
      return this.$store.state.sum;
    }
  }
}
</script>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
