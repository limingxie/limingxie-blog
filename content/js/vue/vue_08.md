---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_条件渲染和遍历渲染(08)"
date: 2022-06-06T23:05:49+08:00
tags: [
    "vue",
    "if",
    "for",
]
categories: [
    "vue",
]
---

这一篇整理了渲染相关的内容。  

## 1.条件渲染

* v-show  
* v-if  
* v-else-if  
* v-else  

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
</head>
<body>
    <div id="app">
        <!-- 使用 v-show 做条件渲染 只是把display = false -->
        <h3 v-show="true">{{name}}</h3>
        <h3 v-show="1===2">{{name}}</h3>

        <!-- v-if 直接把元素去掉 -->
        <h3 v-if="true">{{name}}</h3>
        <h3 v-if="1===2">{{name}}</h3>

        <hr />

        <h3>现在n:{{n}}</h3>
        <button @click="n++">点我+1</button>
        <div v-if="n%3 === 1">小明</div>
        <div v-else-if="n%3 === 2">小红</div>
        <div v-else>小张</div>

        <!-- template v-if 配合使用  v-show是不能跟 template一起用 -->
        <!-- <template v-show="n%3===1"> -->
        <template v-if="n%3===1">
            <h3>小明</h3>
            <h3>小红</h3>
            <h3>小张</h3>
        </template>

    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                name: '张三',
                n:0
            }
        });
        
    </script>
</body>

</html>
```

## 2.遍历渲染

* v-for="(p, index) in persons"
* v-for="(p, index) of persons"

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
</head>
<!-- 
    使用v-for指令可以遍历对象，数组，字符串，指定次数
 -->
<body>
    <div id="app">
        <h3>{{name}}</h3>

        //遍历数组
        <ul>
            <li v-for="p in persons" :key="p.id">
                {{p.name}}-{{p.age}}
            </li>
        </ul>

        
        <!-- 
            <ul>
                <li v-for="(p, index) in persons" :key="index">
                    {{p.name}}-{{p.age}}
                </li>
            </ul> 
        -->

        //遍历对象
        <ul>
            <li v-for="(value,key) in car" :key="key">
                {{key}}-{{value}}
            </li>
        </ul>

        //遍历指定次数
        <ul>
            <li v-for="(number,index) of 5" :key="index">
                {{index}}-{{number}}
            </li>
        </ul>

    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                name: '张三',
                persons: [
                    { id: 1, name: '张三', age: 18 },
                    { id: 2, name: '李四', age: 20 },
                    { id: 3, name: '王五', age: 22 }
                ],
                car:{
                    name:'奥迪',
                    color:'黑色',
                    price:70
                }
            }
        });

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
