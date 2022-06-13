---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_vuex模块化+命名空间namespace(32)"
date: 2022-06-30T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了插槽相关的内容。  

## Count.vue 分组修改

index.js 分组先用起来  
我们观察 index.js，mutations 中有 ​​​JIA​​​、​​JIAN​​​、​​ADD_PERSON​​​，也就是既有计算模块 Count 的方法，也有人员模块 Person 的方法。  
如果以后模块多了都写在这里，不便于管理。其他的 actions、getters、state 都是相同的情况，所以我们进行分组，把计算模块放在一起，把人员管理模块相关配置放一起：  

完整代码如下：  
index.js  

```js
//该文件用于创建vuex中最为核心的store

//引入vuex
import Vuex from 'vuex'
//引入vue
import Vue from "vue";
//应用vuex插件
Vue.use(Vuex)

//求和相关配置
const countOptions = {
    actions: {
        jiaOdd(context, value) {
            console.log("actions中的jianOdd被调用了", context);
            if (context.state.sum % 2) {
                context.commit("JIA", value)
            }
            context.dispatch("demo", value)
        },
        jiaWait(context, value) {
            console.log("actions中的jianWait被调用了");
            setTimeout(() => {
                context.commit("JIA", value)
            }, 500)
        }
    },
    mutations: {
        JIA(state, value) {
            console.log("mutations中的JIA被调用了", state, value);
            state.sum += value;
        },
        JIAN(state, value) {
            console.log("mutations中的JIAN被调用了", state, value);
            state.sum -= value;
        }
    },
    getters: {
        bigSum(state) {
            return state.sum * 10
        }
    },
    state: {
        sum: 0,//当前和
        school: "三里屯小学",
        subject: "Vue",
    }
}
//人员相关配置
const personOptions = {
    actions: {},
    mutations: {
        ADD_PERSON(state, value) {
            console.log("mutations中的ADD_PERSON被调用了", state, value);
            state.personList.unshift(value)
        }
    },
    getters: {},
    state: {
        personList: [
            {id: "001", name: "张三"},
            {id: "002", name: "李四"}
        ]
    }
}

//创建并暴露store
export default new Vuex.Store({
    modules:{
        a: countOptions,
        b: personOptions
    }
});
```

我们可以把 App.vue 中 Person 组件先删掉，只关注 Count.vue ，在 mounted 中打印下 ​​this.$store​​​，找到 state，可以发现有我们刚才写的 a、b  

所以页面上取值就变成了  

```js
<h1>当前求和为：{{ a.sum }}</h1>
<!--    <h3>当前求和10倍为：{{ bigSum }}</h3>-->
    <h3>我在：{{ a.school }}学习{{ a.subject }}</h3>
    <h3 style="color: red">Person组件的总人数为{{b.personList.length}}</h3>
computed: {
    ...mapState(['a','b']),
}
```

优化一下下  
这样写可能有些同学会觉得有点繁琐，每次都 a.sum，a.school，不能像之前那样直接写 sum，shool 吗，可以的，我们修改 index.js 中计算相关配置，增加 ​​​namespaced:true​​，同样的也给人员相关配置增加  

```js
//求和相关配置
const countOptions = {
    namespaced:true,
    ......
}

//人员相关配置
const personOptions = {
    namespaced:true,
    ......
}
```

Count.vue 中

```js
<h1>当前求和为：{{ sum }}</h1>
<!--    <h3>当前求和10倍为：{{ bigSum }}</h3>-->
    <h3>我在：{{ school }}学习{{ subject }}</h3>
    <h3 style="color: red">Person组件的总人数为{{personList.length}}</h3>

computed: {
    ......
    ...mapState('a',['sum','school',"subject","personList"]),
    ...mapState('b',["personList"]),
  ......
  },
```

同样的修改其他 actions、mutations 等，Count.vue 完整代码如下：

```js
<template>
  <div class="category">
    <h1>当前求和为：{{ sum }}</h1>
    <h3>当前求和10倍为：{{ bigSum }}</h3>
    <h3>我在：{{ school }}学习{{ subject }}</h3>
    <h3 style="color: red">Person组件的总人数为{{personList.length}}</h3>
    <select v-model="n">
      <!--这里的value前有冒号，否则value值是字符串-->
      <option :value="1">1</option>
      <option :value="2">2</option>
      <option :value="3">3</option>
    </select>
    <button @click="increment(n)">+</button>
    <button @click="decrement(n)">-</button>
    <button @click="incrementOdd(n)">当前和为奇数再加</button>
    <button @click="incrementWait(n)">等一等再加</button>
  </div>
</template>

<script>
import {mapState,mapGetters,mapMutations,mapActions} from 'vuex'

export default {
  name: "Count",
  data() {
    return {
      n: 1,//用户选择的数字
    }
  },
  computed: {
    ...mapState('a',['sum','school',"subject","personList"]),
    ...mapState('b',["personList"]),
    ...mapGetters('a',['bigSum'])
  },
  methods: {
    ...mapMutations('a',{"increment":"JIA","decrement":"JIAN"}),

    ...mapActions('a',{incrementOdd:"jiaOdd",incrementWait:"jiaWait"})
  }
}
</script>

<style scoped>
select, button {
  margin-right: 5px;
}
</style>
```

## Person.vue 分组修改  

为了练习 actions 和 getters 在 index.js 中人员相关配置中也增加方法。我们直接看代码，然后解释  

Person.vue  

```js
<template>
  <div class="category">
    <h1>人员信息</h1>
    <input type="text" placeholder="请输入名字" v-model="name"/>
    <button @click="add">添加</button>
    <button @click="addWang">添加一个姓王的人</button>
    <ul>
      <li v-for="person in personList" :key="person.id">{{person.name}}</li>
    </ul>

    <h3>列表中第一个人的名字：{{firstPersonName}}</h3>
    <h3 style="color: red">Count组件求和为{{sum}}</h3>
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
      this.$store.commit("b/ADD_PERSON",personObj)
      this.name = ""
    },
    addWang(){
      const personObj = {id:nanoid(),name:this.name}
      this.$store.dispatch("b/addPersonWang",personObj)
      this.name = ""
    }
  },
  computed:{
    personList(){
      return this.$store.state.b.personList;
    },
    sum(){
      return this.$store.state.a.sum;
    },
    firstPersonName(){
      return this.$store.getters['b/firstPersonName'];
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

index.js

```js
//求和相关配置
const countOptions = {
  ......  
}
//人员相关配置
const personOptions = {
    namespaced: true,
    actions: {
        addPersonWang(context, value) {
            if (value.name.indexOf("王") === 0) {
                context.commit("ADD_PERSON", value)
            }else {
                alert("添加的人必须姓王")
            }
        }
    },
    mutations: {
        ADD_PERSON(state, value) {
            console.log("mutations中的ADD_PERSON被调用了", state, value);
            state.personList.unshift(value)
        }
    },
    getters: {
        firstPersonName(state) {
            return state.personList[0].name;
        }
    },
    state: {
        personList: [
            {id: "001", name: "张三"},
            {id: "002", name: "李四"}
        ]
    }
}

//创建并暴露store
export default new Vuex.Store({
    modules: {
        a: countOptions,
        b: personOptions
    }
});
```

我们在 index.js 中 actions 中增加了 ​​addPersonWang​​​ 来筛选，只能添加姓王的人，getters 中增加了 ​​firstPersonName​​ 来返回人员列表第一个人名  

Person.vue 中增加了一个 h3 标签来展示人员列表第一个人的名字，使用计算属性 ​​firstPersonName​​​ return ​​this.$store.getters['b/firstPersonName']​​​来调用了 index.js 中写的返回第一个人名的方法，为什么这么取值呢？我们可以在 mounted 生命钩子中打印 ​​this.$store ​​​查看 getters：  

又增加了一个按钮绑定方法​​​addWang​​​来只添加姓王的人，这个方法写在 action 中，action 联系 mutations 用 ​​dispatch​​​，方法要这样写 ​​b/addPersonWang​​  

继续优化，分成文件引入  
store 下新建 count.js 和 person.js ，把 index.js 中计算相关配置和人员相关配置放别放进来  

在 index.js 中引入即可  

actions联系后端api练习  

Person.vue 中增加一个按钮，添加一个人，名字随机，由后台返回。用到的接口是：​​​<https://api.uixsj.cn/hitokoto/get?type=social>​​，它会随机返回一句话  

person.js

```js
import axios from "axios";
import {nanoid} from "nanoid";

export default {
    namespaced: true,
    actions: {
        ......
        addPersonServer(context) {
            axios.get("https://api.uixsj.cn/hitokoto/get?type=social").then(
                response => {
                    context.commit("ADD_PERSON", {id: nanoid(), name: response.data})
                },
                error => {
                    alert(error.message)
                }
            )
        }
    },
    ......
}
```

Person.vue 中

```js
<button @click="addPersonServer">添加一个人，名字随机</button>
<script>
methods:{
    ......
    addPersonServer(){
      this.$store.dispatch("b/addPersonServer")
    },
  },
</script>
```

## 总结

1、目的：让代码更好维护，让多种数据分类更加明确
2、修改 index.js

```js
const countAbout = {
  namespaced:true,//开启命名空间
  state:{x:1}，
  mutations:{ ... },
  actions:{ ... },
  getters: {
    bigSum( state){
      return state.sum *10
    }
  }
}

const personAbout = {
  namespaced:true,//开启命名空间
  state:{ ... },
  mutations: { ... },
  actions: { ... }
}
  
const store = new Vuex.Store({
  modules: {
    countAbout,
    personAbout
  }
})
```

3、开启命名空间后，组件中读取 state 数据：

```js
//方式一:自己直接读取
this.$store.state.personAbout.list
//方式二:借助mapState读取
...mapState('countAbout',['sum','school','subject']),
```

4、开启命名空间后，组件中读取 getters 数据：

```js
//方式一:自己直接读取
this.$store.getters['personAbout/firstPersonName']
//方式二:借助mapGetters读取：
...mapGetters('countAbout',['bigSum'])
```

5、开启命名空间后，组件中调用 dispatch

```js
//方式一:自己直接dispatch
this.$store.dispatch('personAbout/addPersonwang',person)
//方式二:借助mapActions：
...mapActions ('countAbout' ,{incrementOdd:'jiaOdd',incrementWait:'jiawait'})
```

6、开启命名空间后，组件中调用 commit

```js
//方式一:自己直接 commit
this.$store.commit('personAbout/ADD_PERSON",person)
//方式二:借助 mapMutations：
...mapMutations('countAbout' ,{increment:'JIA',decrement:'JIAN'})，
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
