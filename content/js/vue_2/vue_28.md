---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_请求搜索案例(28)"
date: 2022-06-26T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇做了一个简单的用户搜索的请求案例。  

1、public 下新建 css 文件夹，放入下载好的 bootstrap.css，并在 index.html 中引入  

2、新建 Search.vue  

```js
<template>
  <section class="jumbotron">
    <h3 class="jumbotron-heading">Search Github Users</h3>
    <div>
      <input type="text" placeholder="enter the name you search"/>&nbsp;<button>Search</button>
    </div>
  </section>
</template>

<script>
export default {
  name: "Search"
}
</script>
```

3、新建 List.vue

```js
<template>
  <div class="row">
    <div class="card">
      <a href="" target="_blank">
        <img src="https://cn.vuejs.org/images/logo.svg" style="width: 100%"/>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  name: "List"
}
</script>

<style scoped>
.album {
  min-height: 50rem; /*Can be removed; just added for demo purposes*/
  padding-top: 3rem;
  padding-bottom: 3rem;
  background-color: #f7f7f7;
}

.card {
  float: left;
  width: 33.333%;
  padding: .75rem;
  margin-bottom: 2rem;
  border: 1px solid #efefef;
  text-align: center;
}

.card > img {
  margin-bottom: .75rem;
  border-radius: 100px;
}

.card-text {
  font-size: 85%;
}
</style>
```

4、App.vue 中引入并注册组件

```js
<template>
  <div class="container">
    <Search/>
    <List/>
  </div>
</template>

<script>
import Search from "@/components/Search";
import List from "@/components/List";

export default {
  name: 'App',
  components: {Search,List},
}
</script>
```

请求数据  
1、Search 中请求数据  
我们使用接口：​​​<https://api.github.com/search/users?q=xxx>​​  

请求上边的接口，传入关键字，用 axios 请求，返回数据如下：  

我们关注以下数据即可  

2、传递数据  
我们在子组件 Search.vue 中写网络请求得到数据，需要在子组件 List 中展示，组件之间传递数据，这就用到了我们之前学的全局事件总线，确保 main.js 中安装了全局事件总线  

List 中需要数据，我们就需要绑定事件，当 Search 得到数据触发事件后，List 回调就拿到了数据，然后循环遍历即可，  

```js
<template>
  <div class="row">
    <div class="card" v-for="user in users" :key="user.login">
      <a :href="user.html_url" target="_blank">
        <img :src="user.avatar_url" style="width: 100%"/>
      </a>
      <p class="card-text">{{user.login}}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "List",
  data(){
    return{
      users:[]
    }
  },
  mounted() {
    this.$bus.$on("getUsers",(users)=>{
      console.log("我是List组件，收到数据："+users);
      this.users = users
    })
  }
}
</script>

<style scoped>
.album {
  min-height: 50rem; /*Can be removed; just added for demo purposes*/
  padding-top: 3rem;
  padding-bottom: 3rem;
  background-color: #f7f7f7;
}

.card {
  float: left;
  width: 33.333%;
  padding: .75rem;
  margin-bottom: 2rem;
  border: 1px solid #efefef;
  text-align: center;
}

.card > img {
  margin-bottom: .75rem;
  border-radius: 100px;
}

.card-text {
  font-size: 85%;
}
</style>
```

Search.vue 中网络请求数据后，触发事件

```js
<template>
  <section class="jumbotron">
    <h3 class="jumbotron-heading">Search Github Users</h3>
    <div>
      <input type="text" placeholder="enter the name you search" v-model="keyword"/>&nbsp;
      <button @click="searchUsers">Search</button>
    </div>
  </section>
</template>

<script>
import axios from 'axios'
export default {
  name: "Search",
  data(){
    return{
      keyword:''
    }
  },
  methods:{
    searchUsers(){
      axios.get(`https://api.github.com/search/users?q=${this.keyword}`).then(
          response =>{
            console.log("请求成功了",response.data.items);
            this.$bus.$emit("getUsers",response.data.items)
          },
          error=>{
            console.log("请求失败了",error.message);
          }
      )
    }
  }
}
</script>
```

完善
1、当一进页面需要展示欢迎语
2、搜索过程中展示loading
3、搜索结束展示用户列表
4、搜索返回错误展示错误信息

在 List 中增加变量表示这些状态

```js
<template>
  <div class="row">
    <!--展示用户列表-->
    <div v-show="info.users.length" class="card" v-for="user in info.users" :key="user.login">
      <a :href="user.html_url" target="_blank">
        <img :src="user.avatar_url" style="width: 100%"/>
      </a>
      <p class="card-text">{{ user.login }}</p>
    </div>
    <!--展示欢迎词-->
    <h1 v-show="info.isFirst">欢迎使用</h1>
    <!--展示loading-->
    <h1 v-show="info.isLoading">加载中...</h1>
    <!--展示错误信息-->
    <h1 v-show="info.errMsg">{{ info.errMsg }}</h1>
  </div>
</template>

<script>
export default {
  name: "List",
  data() {
    return {
      info: {
        isFirst: true,
        isLoading: false,
        errMsg: '',
        users: []
      }
    }
  },
  mounted() {
    this.$bus.$on("updateListData", (dataObj) => {
      console.log("我是List组件，收到数据：" + dataObj);
      //因为Search中isFirst第二次没有传，this.info = dataObj 这样写会丢失isFirst
      //ES6语法，这样写dataObj更新this.info中数据，dataObj没有的以this.info中为准
      this.info = {...this.info,...dataObj}
    })
  }
}
</script>

<style scoped>
.album {
  min-height: 50rem; /*Can be removed; just added for demo purposes*/
  padding-top: 3rem;
  padding-bottom: 3rem;
  background-color: #f7f7f7;
}

.card {
  float: left;
  width: 33.333%;
  padding: .75rem;
  margin-bottom: 2rem;
  border: 1px solid #efefef;
  text-align: center;
}

.card > img {
  margin-bottom: .75rem;
  border-radius: 100px;
}

.card-text {
  font-size: 85%;
}
</style>
```

Search.vue 中改变这些状态

```js
<template>
  <section class="jumbotron">
    <h3 class="jumbotron-heading">Search Github Users</h3>
    <div>
      <input type="text" placeholder="enter the name you search" v-model="keyword"/>&nbsp;
      <button @click="searchUsers">Search</button>
    </div>
  </section>
</template>

<script>
import axios from 'axios'
export default {
  name: "Search",
  data(){
    return{
      keyword:''
    }
  },
  methods:{
    searchUsers(){
      //请求前更新 List 数据
      this.$bus.$emit("updateListData",{isFirst:false,isLoading:true,errMsg:'',users:[]})
      axios.get(`https://api.github.com/search/users?q=${this.keyword}`).then(
          response =>{
            console.log("请求成功了",response.data.items);
            //请求成功后更新 List 数据
            this.$bus.$emit("updateListData",{isLoading:false,errMsg:'',users:response.data.items})
          },
          error=>{
            console.log("请求失败了",error.message);
            //请求失败更新 List 数据
            this.$bus.$emit("updateListData",{isLoading:false,errMsg:error.message,users:[]})
          }
      )
    }
  }
}
</script>
```

使用 vue-resource
vue-resource 是一个非常轻量的用于处理 HTTP 请求的插件。  
Vue2.0之后，就不再对 vue-resource 更新，而是推荐使用 axios。因为目前我们还在学 Vue2，所以还是学习一下  

1、执行​​npm i vue-resource​​​安装  
2、main.js 引入并使用  

```js
......
//引入vue-resource
import vueResource from 'vue-resource'

......
//使用vue-resource插件
Vue.use(vueResource)

......
```

我们可以不引入 axios，axios.get 改为 this.$http.get 即可  

这里只是了解使用 vue-resource，还是推荐使用 axios  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
