---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_计算属性(05)"
date: 2022-06-03T23:45:49+08:00
tags: [
    "vue",
    "computed",
]
categories: [
    "vue",
]
---

这篇整理了Vue的计算属性<!--more-->

## 1.定义

要用的属性不存在，需要通过已有的属性计算得出。

## 2.原理

底层借助Object.defineproperty()方法提供的getter()和setter()方法，

## 3.getter()和setter()方法的作用

#### getter()方法

当访问初次计算属性时或属性值有变化时，会调用getter()方法。

#### setter()方法

当设置计算属性时，会调用setter()方法，

## 4.优势

比methods方法，内部有缓存机制

## 5.备注

1.计算属性最终会出现在vm上，可以直接读取
2.如果属性被改会自动调用get方法，想改计算属性，需要再set里编写相对应的逻辑

## 6.实例

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
        姓：<input type="text" v-model="firstName" /> <br /> <br />
        名：<input type="text" v-model="lastName" /> <br /> <br />
        <!-- 插值语法 -->
        <!-- 姓名：<span>{{firstName + "-" + lastName}}</span> -->
        <!-- 姓名：<span>{{firstName}}-{{lastName}}</span> -->
        <!-- 姓名：<span>{{firstName.slice(0,3)}}-{{lastName}}</span> -->
        <!-- methods语法 -->
        <!-- 姓名：<span>{{fullName()}}</span> -->
        <!-- 计算属性 -->
        姓名：<span>{{fullName}}</span>
        <!-- 第二次会调用缓存 -->
        姓名：<span>{{fullName}}</span>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                firstName: "张",
                lastName: "三",
            },
            //计算属性，计算属性是需要配置一个对象
            computed: {
                fullName: {
                    //只要有人读取fullName，就会调用这 个函数 
                    //get调用时 1.初次读取的时候，2.依赖的数据有变化
                    get() {
                        return this.firstName + "-" + this.lastName;
                    },
                    //fullName被修改的时候调用
                    set(value) {
                        let arr = value.split("-");
                        this.firstName = arr[0];
                        this.lastName = arr[1];
                    }
                },
                //当确认只有get方法直接可以写函数,当做get方法
                fullName_jianxie: function () {
                    return this.firstName + "-" + this.lastName;
                },
                // methods: {
                //     fullName() {
                //         return this.firstName + "-" + this.lastName;
                //     }
                // }
            }
        });

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
