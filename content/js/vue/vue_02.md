---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_数据绑定和MVVM模型"
date: 2022-05-31T09:05:49+08:00
tags: [
    "vue",

]
categories: [
    "vue",
]
---

看vue数据绑定的几种方式。  

#### 1.单项双向绑定

**v-bind**: 单项绑定，数据只能是从data流向页面  
**v-model**: 双项绑定，数据可以从页面流向data,也可以从data流向页面  
v-model 指令只能用在表单类元素(输入类)  
v-model:value 可以简写为 v-model  

#### 2.el和data的两种写法

**el**

1. 可以 new的时候配置  
2. 先创建实例，然后再用$mount指定  

**data**  

1. 对象式：可以是对象  
2. 函数式：函数必须返回object类型。也不能是箭头函数，因为Vue管理的函数如果是箭头函数的话，this指向不是Vue实例。  

#### 3.实例

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
        单项数据绑定: <input type="text" v-bind:value="name"><br />
        双项数据绑定: <input type="text" v-model:value="name"><br />
        <!-- 
            v-bind: 单项绑定，数据只能是从data流向页面
            v-model: 双项绑定，数据可以从页面流向data,也可以从data流向页面
                v-model 指令只能用在表单类元素(输入类)
                v-model:value 可以简写为 v-model 
        -->
        ---------简写--------<br />
        <!-- 简写 -->
        单项数据绑定: <input type="text" :value="name"><br />
        双项数据绑定: <input type="text" v-model="name"><br />
    </div>

    <div id="app1">
        <h1>你好，{{name}}</h1>
    </div>


    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',  //vue和容器关联
            data: {     //存储数据，提供给el的容器。 这是对象和函数。
                name: '小明',
                website: {
                    name: '百度',
                    url: 'http://www.baidu.com'

                }
            }
        });

        /*
            vue的 
                el 属性   
                    1.可以 new的时候配置
                    2.先创建实例，然后再用$mount指定
                data 属性
                    1.对象式：可以是对象
                    2.函数式：函数必须返回object类型。 
                        也不能是箭头函数，因为Vue管理的函数如果是箭头函数的话，this指向不是Vue实例了

        */

        const v = new Vue({
            // el: '#app1', 
            // 函数必须要返回对象。
            data: function () { //此处的this是vue实例对象，所以不能用箭头函数
                return {
                    name: '小红',
                    website: {
                        name: '谷歌',
                        url: 'https://google.com'

                    }
                }
            }
        });

        console.log(v);

        //可以指定容器
        // v.$mount('#app1');

        setTimeout(() => {
            v.$mount('#app1');
        }, 2000)

    </script>
</body>

</html>
```

#### 4.MVVM 模型

看看Vue官网的这段话。

>然没有完全遵循 MVVM 模型，但是 Vue 的设计也受到了它的启发。
>因此在文档中经常会使用 vm (ViewModel 的缩写) 这个变量名表示 Vue 实例。

**M**: 模型Model ==> 对应的是data中的数据  
**V**: 视图View ==> 模板  
**VM**: 视图模型ViewModel ==> Vue实例对象  

[图片备用地址](https://limingxie.github.io/images/js/vue/MVVM.png)  
![vue_mvvm](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/MVVM.png?x-oss-process=image/resize,w_700,m_lfit)

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
</head>
<body>
    <!-- 整个div容器就是VIEW -->
    <div id="app">
        <h3> hello {{name}} </h3>
        <h3> {{_c}} </h3> <!-- 只要vm身上在的属性都可以使用 -->
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        const vm = new Vue({ //=> 实例化Vue是 View-Model
            el: '#app', 
            data: { //=> data数据是 Model
                name: 'Vue!'
            }
        });
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
