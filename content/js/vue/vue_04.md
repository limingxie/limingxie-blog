---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_数据代理"
date: 2022-06-01T09:05:49+08:00
tags: [
    "vue",

]
categories: [
    "vue",
]
---

## 1.Object.defineproperty

理解数据代理之前，先需要理解Object.defineProperty

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script type="text/javascript">
        let number = 18;
        let Person = {
            name: "小明",
            address: "北京"
        }

        Object.defineProperty(Person, "age", {
            // value:18,
            // enumerable: true,//控制属性是否可以枚举，默认false 不能在对象遍历中查看
            // writable:true, //控制属性是否能修改，默认false
            // configurable:true, //控制属性是否能删除，默认false

            //当读取Person age属性时，get函数(getter)会被调用，且返回age值。
            get: function () {
                console.log('取age值');
                return number;
            },
            //当修改Person age属性时，set函数(setter)会被调用，且传入的参数是新的age值。
            set(value) {
                console.log('修改age值:', value);
                number = value;
            }
        })

    </script>
</body>

</html>
```

## 2.数据代理

基于上述的Object.defineproperty功能，我们继续了解一下数据代理。  

> 通过一个对象代理对另一个对象中属性的(读写)操作。

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <!-- 通过obj2 代理 obj1 的 数据 -->
    <script type="text/javascript">
        let obj1 = { x: 10 }
        let obj2 = { y: 20 }

        Object.defineProperty(obj2, 'x', {
            get: function () {
                return obj1.x;
            },
            set: function (value) {
                obj1.x = value
            }
        })

    </script>
</body>

</html>
```

## 3.Vue中的数据代理

#### 3.1 Vue中的数据代理  

通过vm对象来代理data对象中属性(读写)操作  

#### 3.2 Vue中数据代理的好处  

更方便的操作data中的数据  

#### 3.3 基本原理  

通过Object.defineProperty()把data对象中的所有属性添加到vm上  
为每一个添加到vm上的属性，都会有一个getter和一个setter  
在getter/setter内部去操作data对象中的属性  

#### 3.4 实现数据代理的原理

[图片备用地址](https://limingxie.github.io/images/js/vue/data_proxy.png)  
![data_proxy](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/data_proxy.png?x-oss-process=image/resize,w_800,m_lfit)

#### 3.5 代码实例

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
        <h3>姓名：{{name}}</h3>
        <h3>年龄：{{age}}</h3>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        let aa = {
            name: '小明',
            age: 18
        };

        //vm._data 为vue实例对象的data属性
        const vm = new Vue({
            el: '#app',
            // data: {
            //     name: '小明',
            //     age: 18
            // }
            data: aa
        });
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
