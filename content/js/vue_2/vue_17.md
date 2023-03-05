---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_组件(17)"
date: 2022-06-15T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了组件相关的内容。  <!--more-->

## 非单文件组件

一个文件中包含 n 个组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue初识</title>
    <script type="text/javascript" src="./js/vue.js"></script>
</head>
<body>
<div id="root">
    <!--第三步：编写组件标签-->
    <xuexiao></xuexiao>
    <hr>
    <student></student>
</div>

<div id="root2">
    <hello></hello>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false

    //第一步：创建学校组件
    const school = Vue.extend({
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校地址：{{ address }}</h2>
          <button @click="showName">点我提示学校名</button>
          </div>
        `,
        //组件定义时不要写el，因为最终所有组件都要被一个vm管理，由vm决定服务于哪个容器
        //el: "#root",
        data() {
            return {
                schoolName: "三里屯小学",
                address: "北京"
            }
        },
        methods: {
            showName() {
                alert(this.schoolName)
            }
        }
    })
    //第一步：创建学生组件
    const student = Vue.extend({
        template: `
          <div>
          <h2>学生姓名：{{ studentName }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          </div>
        `,
        data() {
            return {
                studentName: "张三",
                age: 7
            }
        }
    })

    //第一步：创建hello组件
    const hello = Vue.extend({
        template: `
          <div>
          <h2>你好：{{ name }}</h2>
          </div>
        `,
        data() {
            return {
                name: "Errol"
            }
        }
    })

    //第二步：全局注册组件
    Vue.component('hello', hello)

    //创建vue实例
    new Vue({
        el: "#root",
        //第二步：注册组件（局部注册）
        components: {
            xuexiao: school,
            student
        }
    })

    //创建vue实例
    new Vue({
        el: "#root2"
    })

</script>
</body>
</html>
```

#### 组件使用步骤

**Vue中使用组件的三大步骤**  
一、定义组件(创建组件)  
二、注册组件  
三、使用组件(写组件标签)  

**如何定义一个组件?**  
使用​​​Vue.extend(options)​​​创建，其中​​options​​​和​​new Vue(options)​​​时传入的那个​​options​​​几乎一样，但也有点区别。  
区别如下:  
1.​​​el​​​不要写，为什么?  
因为最终所有的组件都要经过一个 vm 的管理，由 vm 中的 el 决定服务哪个容器  
2.​​​data​​​必须写成函数，为什么?  
因为避免组件被复用时，数据存在引用关系  
备注：使用​​​template​​可以配置组件结构  

**二、如何注册组件?**  
1.局部注册：靠​​​new Vue​​​的时候传入​​components​​​选项  
2.全局注册：靠​​​Vue.component('组件名’,组件)​​  

#### 总结

**1.关于组件名:**  
一个单词组成：  
第一种写法(首字母小)：school  
第二种写法(首字母大写)：School  

多个单词组成：  
第一种写法(kebab-case命名)：my-school  
第二种写法(CamelCase命名)：MySchool（需要Vue脚手架支持)  

备注  
(1).组件名尽可能回避HTML中已有的元素名称，例如:h2、H2都不行  
(2).可以使用 name 配置项指定组件在开发者工具中呈现的名字  

**2.关于组件标签:**  

```
第一种写法: ​​​<school></school>  ​​​
第二种写法:​​<school/>  ​​
备注：不用使用脚手架时,​​<schoo1/>​​会导致后续组件不能渲染  
```

**3.一个简写方式：**  
​​​const school = Vue.extend(options）​​​可简写为：​​const school = options​​  

```js
//第一步：创建学校组件
    const school = {
        name:'info',
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校地址：{{ address }}</h2>
          </div>
        `,
        data() {
            return {
                schoolName: "三里屯小学",
                address: "北京"
            }
        }
    }
```

#### 组件嵌套

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue初识</title>
    <script type="text/javascript" src="./js/vue.js"></script>
</head>
<body>
<div id="root">
<app></app>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    //定义学生组件
    const student = Vue.extend({
        template: `
          <div>
          <h2>学生名称：{{ name }}</h2>
          <h2>学生年龄：{{ age }}</h2>
          </div>
        `,
        data() {
            return {
                name: '张三',
                age: 7
            }
        },
    })
    //定义学校组件
    const school = Vue.extend({
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校地址：{{ address }}</h2>
          <student></student>
          </div>
        `,
        data() {
            return {
                schoolName: '三里屯小学',
                address: '北京'
            }
        },
        components: {
            student
        }
    })
    //定义hello组件
    const hello = Vue.extend({
        template: `
          <div>
          <h2>你好{{name}}</h2>
          </div>
        `,
        data() {
            return {
                name: 'Errol'
            }
        }
    })

    //创建app组件
    const app = Vue.extend({
        template: `
          <div>
          <school></school>
          <hello></hello>
          </div>
        `,
        components: {
            school,
            hello
        }
    })

    new Vue({
        el: '#root',
        components: {
            app
        }
    })
</script>
</body>
</html>
```

有一点注意，标准化开发时，建议使用 app 管理其他组件。如果不想在页面中写 app 标签，可以在 new Vue 时增加 template 标签：  

```html
<!DOCTYPE html>
<html lang="en">
<head>
......
</head>
<body>
<div id="root">
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    ......
    new Vue({
        template:'<app></app>',
        el: '#root',
        components: {
            app
        }
    })
</script>
</body>
</html>
```

#### VueComponent

1. school 组件本质是一个名为 VueComponent 的构造函数，且不是程序员定义的，是​​Vue.extend​​ 生成的  
2. 我们只需要写​​<school/>​​​或​​<school></school>​​​，Vue 解析时会帮我们创建 school 组件的实例对象，即 vue 帮我们执行的:​​new vuecomponent(options)​​  
3. 特别注意：每次调用​​Vue.extend​​​，返回的都是一个全新的​​VueComponent​​!!!  
4. 关于​​this​​​指向  
(1).组件配置中:  
data 函数、methods 中的函数、watch 中的函数、computed中的函数它们的this均是​​​VueComponent实例对象​​​  
(2).new Vue(options) 配置中:
data 函数、methods 中的函数、watch 中的函数、computed 中的函数它们的 this 均是​​Vue实例对象​​  
5. VueComponent的实例对象，以后简称 vc（也可称之为:组件实例对象）  
Vue的实例对象，以后简称vm  

#### 内置关系

**一个重要的内置关系：**  ​​​

```
VueComponent. prototype.**proto** === Vue.prototype​​
```

可以用以下代码来验证

```js
console.log(school.prototype.**proto** === Vue.prototype);
```

[图片备用地址](https://limingxie.github.io/images/js/vue/VueComponet.png)  
![VueComponet](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/VueComponet.png)

```html
<script type="text/javascript">
    Vue.config.productionTip = false
    //定义一个构造函数
    function Demo() {
        this.a = 1
        this.b = 2
    }
    //创建demo的一个实例对象
    const d = new Demo()

    console.log(Demo.prototype);//显式原型属性
    console.log(d.__proto__);//隐式原型属性
    console.log(Demo.prototype === d.__proto__);//隐式原型属性

    //通过显式原型属性操作 原型对象，追加x属性为99
    Demo.prototype.x = 99
    console.log(d);
    console.log(d.x);
    console.log(d.__proto__.x);
</script>
```

2.为什么要有这个关系：让组件实例对象(vc）可以访问到 Vue 原型上的属性、方法
我们可以用之前的例子验证，写一个 school 组件，然后给 Vue 增加一个 x 属性，然后让 school 来调用

```html
<div id="root">
    <school></school>
</div>

<script type="text/javascript">
    Vue.config.productionTip = false
    Vue.prototype.x = 99
    //第一步：创建学校组件
    const school = Vue.extend({
        template: `
          <div>
          <h2>学校名称：{{ schoolName }}</h2>
          <h2>学校地址：{{ address }}</h2>
          <button @click="showX">点我输出x</button>
          </div>
        `,
        data() {
            return {
                schoolName: "三里屯小学",
                address: "北京"
            }
        },
        methods:{
            showX(){
                alert(this.x)
            }
        }
    })

    //创建vue实例
    new Vue({
        el: "#root",
        //第二步：注册组件（局部注册）
        components: {
            school
        }
    })
</script>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
