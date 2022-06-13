---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_插槽(29)"
date: 2022-06-27T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了插槽相关的内容。

## 默认插槽  

新增 Category.vue  

```js
<template>
<div class="category">
  <h3>{{title}}分类</h3>
  <ul>
    <li v-for="(item,index) in listData" :key="index">{{item}}</li>
  </ul>
</div>
</template>

<script>
export default {
  name: "Category",
  props:["title","listData"]
}
</script>

<style scoped>
.category{
  background-color: skyblue;
  width: 200px;
  height: 300px;
}
h3{
  text-align: center;
  background-color: orange;
}
</style>
```

App.vue 中使用

```js
<template>
  <div class="container">
    <Category title="美食" :listData="foods"/>
    <Category title="游戏" :listData="games"/>
    <Category title="电影" :listData="films"/>
  </div>
</template>

<script>
import Category from "@/components/Category";

export default {
  name: 'App',
  components: {Category},
  data(){
    return{
      foods:["火锅","烧烤","小龙虾","牛排"],
      games:["劲舞团","QQ飞车","超级玛丽","无人深空"],
      films:["《教父》","《狩猎》","《禁闭岛》","《聚焦》"]
    }
  }

}
</script>

<style>
.container {
  display: flex;
  justify-content: space-around;
}
</style>
```

刚才每个分类要展示的信息，样式都是一样的，所以通过传值就可以实现。现在修改需求，每个分类都要展示不同的内容：  

这里就用到了插槽。插槽是给组件预留的空间，插槽如何使用由父组件决定。修改 Category.vue  

```js
<template>
<div class="category">
  <h3>{{title}}分类</h3>
  <slot></slot>
</div>
</template>

<script>
export default {
  name: "Category",
  props:["title"]
}
</script>

<style scoped>
.category{
  background-color: skyblue;
  width: 200px;
  height: 300px;
}
h3{
  text-align: center;
  background-color: orange;
}
</style>
```

修改 App.vue

```js
<template>
  <div class="container">
    <Category title="美食" :listData="foods">
      <img src="https://img2.baidu.com/it/u=2073611229,1921777437&fm=253&fmt=auto&app=120&f=JPEG?w=1200&h=675"/>
    </Category>
    <Category title="游戏" :listData="games">
      <ul>
        <li v-for="(g,index) in games" :key="index">{{g}}</li>
      </ul>
    </Category>
    <Category title="电影">
      <video controls src="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"/>
    </Category>
  </div>
</template>

<script>
import Category from "@/components/Category";

export default {
  name: 'App',
  components: {Category},
  data(){
    return{
      foods:["火锅","烧烤","小龙虾","牛排"],
      games:["劲舞团","QQ飞车","超级玛丽","无人深空"],
      films:["《教父》","《狩猎》","《禁闭岛》","《聚焦》"]
    }
  }

}
</script>

<style>
.container {
  display: flex;
  justify-content: space-around;
}
img,video{
  width: 100%;
}
</style>
```

## 具名插槽  

具名插槽是当子组件需要显示不同的效果时使用具名插槽，通过 name 属性给插槽命名。我们修改 Category.vue  

```js
<template>
<div class="category">
  <h3>{{title}}分类</h3>
  <slot name="center">这是一些默认值，没有内容时展示</slot>
  <slot name="footer">这是一些默认值，没有内容时展示</slot>
</div>
</template>

<script>
export default {
  name: "Category",
  props:["title"]
}
</script>

<style scoped>
.category{
  background-color: skyblue;
  width: 200px;
  height: 300px;
}
h3{
  text-align: center;
  background-color: orange;
}
</style>
```

修改 App.vue

```js
<template>
  <div class="container">
    <Category title="美食" :listData="foods">
      <img slot="center" src="https://img2.baidu.com/it/u=2073611229,1921777437&fm=253&fmt=auto&app=120&f=JPEG?w=1200&h=675"/>
      <a slot="footer" href="https://www.baidu.com">更多美食</a>
    </Category>
    <Category title="游戏" :listData="games">
      <ul slot="center">
        <li v-for="(g,index) in games" :key="index">{{g}}</li>
      </ul>
      <div class="foot" slot="footer">
        <a href="https://www.baidu.com">单机游戏</a>
        <a href="https://www.baidu.com">网络游戏</a>
      </div>
    </Category>
    <Category title="电影">
      <video slot="center" controls src="http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"/>
      <!--v-slot 只有template能用-->
      <template v-slot:footer>
        <div class="foot" slot="footer">
          <a href="https://www.baidu.com">经典</a>
          <a href="https://www.baidu.com">热门</a>
          <a href="https://www.baidu.com">推荐</a>
        </div>
      </template>
    </Category>
  </div>
</template>

<script>
import Category from "@/components/Category";

export default {
  name: 'App',
  components: {Category},
  data(){
    return{
      foods:["火锅","烧烤","小龙虾","牛排"],
      games:["劲舞团","QQ飞车","超级玛丽","无人深空"],
      films:["《教父》","《狩猎》","《禁闭岛》","《聚焦》"]
    }
  }

}
</script>

<style>
.container,.foot {
  display: flex;
  justify-content: space-around;
}
img,video{
  width: 100%;
}
</style>
```

#### 作用域插槽  

如果数据在 Category 中，但需要展示不同的形式，我们可以通过插槽传值，类似于我们从父组件向子组件传值：  

```js
<template>
  <div class="category">
    <h3>{{ title }}分类</h3>
    <slot :games="games" :msg="hello"></slot>
  </div>
</template>

<script>
export default {
  name: "Category",
  props: ["title"],
  data() {
    return {
      games: ["劲舞团", "QQ飞车", "超级玛丽", "无人深空"]
    }
  }
}
</script>

<style scoped>
.category {
  background-color: skyblue;
  width: 200px;
  height: 300px;
}

h3 {
  text-align: center;
  background-color: orange;
}
</style>

```

App 中在子组件中使用 ​​<template>​​ 包裹要展示的内容

```js
<template>
  <div class="container">
    <Category title="游戏">
      <template v-slot="receiveValue">
        <ul>
          <li v-for="(g,index) in receiveValue.games" :key="index">{{ g }}</li>
        </ul>
        <a>{{ receiveValue.msg }}</a>
      </template>
    </Category>

    <Category title="游戏">
      <template v-slot="{games,msg}">
        <ol>
          <li v-for="(g,index) in games" :key="index">{{ g }}</li>
        </ol>
        <a>{{ msg }}</a>
      </template>
    </Category>

    <Category title="游戏">
      <template v-slot="{games,msg}">
        <h4 v-for="(g,index) in games" :key="index">{{ g }}</h4>
        <a>{{ msg }}</a>
      </template>
    </Category>

  </div>
</template>

<script>
import Category from "@/components/Category";

export default {
  name: 'App',
  components: {Category},
}
</script>

<style>
.container, .foot {
  display: flex;
  justify-content: space-around;
}

img, video {
  width: 100%;
}
</style>
```

## 插槽总结

1、作用：让父组件可以向子组件指定位置插入 html 结构，也是一种组件问通信的方式，适用于父组件==>子组件  
2、分类：默认插槽、具名插槽、作用域插槽  
3、使用方式：  

#### 默认插槽

父组件中  

```js
<Category>
  <div>html结构</div>
<Category>

子组件中
<template>
  <div>
  <!--定义插槽-->
  <slot>插槽默认内容...</slot>
  </div>
</templdte>
```

#### 具名插槽

父组件中

```js
<Category>
  <template slot="center">
    <div>html结构1</div>
  </template>
  <tenplate v-slot:footer>
    <div>html结构2</div>
  </template>
</Category>
子组件中
<template>
  <div>
  <1--定义插槽-->
  <slot name="center">插槽默认内容...</slot>
  <slot name="footer”>插槽默认内容...</slot>
  </div>
</template>
```

作用域插槽  
1、理解：数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定，(games 数据在 Category 组件中，但使用数据所遍历出来的结构由 App 组件决定)  
2.具体编码：  

父组件中  

```js
<category>
  <template v-slot="scopeData"
  <!--生成的是ul列表-->
  <ul>
    <li v-for="g in scopeDsta.games" : key="g">{g}</li>
  </ul>
  </template>
</Category>

<Category>
  <template v-slot={scopeData}>
  <!--生成的是h4标题-->
  <h4 v-for="g in scopeData" :key="g">{{g}}</h4>
  </template>
</Category>

子组件中
<template>
  <div>
    <slot :games="games"></slot>
  </div>
</template>

<script>
export default{
  name: "Category ',
  props: ["title"],
  //数据在子组件自身
  data() {
    return{
      games:['红色警戒,穿越火线',"劲舞团",超级玛丽"]
    }
  }
}</script>
```

注意​​scope​​​和​​slot-scope​​​过时了，可以使用​​v-slot​​  

#### $slot  

App 中有 Demo 组件，使用插槽  

```js
<template>
  <div>
    <Demo>
      <h2>你好</h2>
    </Demo>
  </div>
</template>
```

在子组件 Demo 中的 ​​mounted​​​ 生命钩子中打印 ​​this​​  

```js
mounted() {
    console.log(this);
}
```

可以看到 ​​$slot​​​中有刚才使用的 h2 标签  
如果在App中使用具名插槽  

```js
<template>
  <div>
    <Demo>
      <template slot="slot1">
        <h2>你好</h2>
      </template>
    </Demo>
  </div>
</template>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
