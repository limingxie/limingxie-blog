---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_基本语法"
date: 2022-05-30T09:05:49+08:00
tags: [
    "vue",

]
categories: [
    "vue",
]
---

看vue的官网文档感觉没有一点前端的基础的看起来很累，  
我希望结合着这个笔记一起看，哪怕是小白能能入门。  

具体Vue的概念，什么特点安装之类的都可以看官网的资料。
vue的中文网站: <https://cn.vuejs.org/>  

这里简单的写一个Vue的实例以及几个需要注意的地方。

#### 实例

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
    <!-- <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js" ></script> -->
</head>

<!-- 
        1.创建一个Vue实例, 需要el和data的作为参数。
        2.Vue指定的容器(div)需要加入一些Vue的语法{{}}。
        3.Vue容器里的代码成为【Vue模板】。 
        4.一个Vue实例和容器是1对1 关系。不能多个Vue实例挂载到同一个容器上。
        5.{{xxx}} 里面的必须是js表达式。可以是变量，也可以是一个有返回值的代码。
        6.Vue实例的数据变了，容器里的值也会自动更新。
        7.真实开发中只有一个Vue实例。配合一些组件一起使用。
    -->

<body>
    <div id="app">
        <h3> 插值语法 </h3> <!-- 只能在标签之间的内容 -->
        <h3> hello, {{ name }} </h3>
        <h5> dateTime: {{ new Date() }} </h5>

        <h3> 指令语法 </h3> <!-- 标签属性，标签体内容，绑定事件等等...-->
        <!-- 添加 v-bind 代表vue的指令 代表“url”是 js表达式-->
        <a v-bind:href="website.url">{{website.name}}一下</a>
        <br>
        <!-- 添加 v-bind 简写-->
        <a :href="website.url">百度一下1</a>
    </div>

    <div id="app1">
        <h1> hello11, {{ name }} </h1>
        <h5> dateTime11: {{ new Date() }} </h5>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',  //vue和容器关联
            data: { //存储数据，提供给el的容器。 这是对象和函数。
                name: 'Vue!',
                website: {
                    name: '百度',
                    url: 'http://www.baidu.com'

                }
            }
        });

        new Vue({
            el: '#app',  //vue和容器关联
            data: { //存储数据，提供给el的容器。 这是对象和函数。
                name: 'Vue111!'
            }
        })

        new Vue({
            el: '#app1',  //vue和容器关联
            data: { //存储数据，提供给el的容器。 这是对象和函数。
                name: 'Vue!'
            }
        })
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
