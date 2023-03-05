---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_自定义指令(15)"
date: 2022-06-13T23:55:49+08:00
tags: [
    "vue",
    "directives",
]
categories: [
    "vue",
]
---

这一篇整理了自定义指令相关的内容。  <!--more-->

#### 函数式

定义一个​​v-big​​​指令，和​​v-text​​功能类似，但会把绑定的数值放大10倍

```html
<div id="app">
    <h2>当前的n值是<span v-text="n"></span></h2>
    <h2>放大10倍后的n值是：<span v-big="n"></span></h2>
    <button @click="n++">点我n+1</button>
</div>
```

```js
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            n:1
        },
        directives:{
            /*big:function () {
            }*/
            //big函数何时会被调用？
            //1、指令与函数成功绑定时（一上来）
            //2、指令所在的模板被重新解析时
            big(element,binding){
                element.innerHTML = binding.value * 10
            }
        }
    })
</script>
```

#### 对象式

定义一个​​v-fbind​​​指令，和​​v-bind​​​功能类似，但可以让其所绑定的​​input​​元素默认获取焦点

```html
<div id="app">
    <input type="text" v-bind:value="n"><br/>
    <input type="text" v-fbind:value="n"><br/>
    <button @click="n++">点我n+1</button>
</div>
```

```js
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            n:1
        },
        directives:{
            fbind:{
                bind(element,binding){
                    //指令与函数成功绑定时（一上来）
                    console.log("bind");
                    element.value = binding.value
                },
                inserted(element,binding){
                    //指令所在元素被插入页面时
                    console.log("inserted");
                    element.focus()
                },
                update(element,binding){
                    //指令所在元素被重新解析时
                    console.log("update");
                    element.value = binding.value
                }
            }
        }
    })
</script>
```

#### 需要注意的地方

**命名**  
如果指令是多个单词：  

<h2>放大10倍后的n值是：<span v-big-number="n"></span></h2>
1.
那么需要这样写：

```html
'big-number':function (element,binding){
  element.innerHTML = binding.value * 10
}
```

或者

```html
'big-number' (element,binding){
  element.innerHTML = binding.value * 10
}
```

**this**  

```html
directives:{
            fbind:{
                bind(element,binding){
                    //此处的this是window
                    console.log("bind");
                    element.value = binding.value
                }
            }
        }
```

#### 全局指令

像过滤器一样，我们把刚才的两个指令改为全局指令

```html
Vue.directive('big', function (element, binding) {
        element.innerHTML = binding.value * 10
    })
```

```html
Vue.directive('fbind',{
        bind(element,binding){
            element.value = binding.value
        },
        inserted(element,binding){
            element.focus()
        },
        update(element,binding){
            element.value = binding.value
        }
    })
```

#### 总结

一、定义语法:

(1).局部指令

```js
new Vue({
  directives:{
    指令名:配置对象
  }
})
```

```js
new Vue({
  directives:{
    指令名:回调函数
  }
})
```

(2).全局指令  

​​​Vue.directive(指令名,配置对象)​​​或 ​​Vue.directive(指令名,回调函数)​​  

二、配置对象中常用的3个回调:  
(1).bind：指令与元素成功绑定时调用  
(2).inserted：指令所在元素被插入页面时调用  
(3).update：指令所在模板结构被重新解析时调用  

三、备注  
1.指令定义时不加​​​v-​​​，但使用时要加​​v-​​​  
2.指令名如果是多个单词，要使用​​kebab-case​​命名方式，不要用​​camelCase​​命名  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
