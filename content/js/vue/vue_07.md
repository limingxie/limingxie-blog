---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_样式(7)"
date: 2022-06-05T23:05:49+08:00
tags: [
    "vue",
    "class",
    "style",
]
categories: [
    "vue",
]
---

这一篇整理了样式相关的内容。  
绑定class和绑定style。

#### 实例

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
        <!-- 绑定class 字符写法：绑定不确定样式class -->
        <div class="basic" :class="mod" @click="changemod">{{name}}</div><br/><br/>
        <!-- 绑定class 数组写法：绑定class样式 可以放数组 -->
        <div class="basic" :class="classArr" @click="changemod">{{name}}</div><br/><br/>
        <!-- 绑定class 对象写法：可以绑定对象，给true，false 判断适不适用 -->
        <div class="basic" :class="classArr" @click="changemod">{{name}}</div><br/><br/>

        <!-- 绑定style 对象写法 -->
        <!-- <div class="basic" :style={fontSize: fsize+'px'}>{{name}}</div><br/><br/> -->
        <div class="basic" :style="styleObj">{{name}}</div><br/><br/>
        <!-- 绑定style 数组写法 -->
        <div class="basic" :style="[styleObj,styleObj1]">{{name}}</div><br/><br/>

    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
       new Vue({
            el: '#app',
            data: {
                name: '张三',
                mode: 'nomarl',
                classArr:[
                    'sad',
                    'happy',
                    ...
                ],
                classObj:{
                    nomarl:true,
                    happy:false,
                    sad:false,
                },
                // fsize: 40,
                styleObj: {
                    fontSize: '40px'
                },
                styleObj1: {
                    color: 'red'
                }
                
            },
            methods: {
                changemod() {
                    this.mod = 'happy';
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
