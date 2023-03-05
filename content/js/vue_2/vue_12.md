---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_收集表单数据(12)"
date: 2022-06-10T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了收集表单数据相关的内容。  <!--more-->

#### 收集表单数据

```
若：<input type="text"/>， 则v -model收集的是 value 值，用户输入的就是 value 值  
若：<input type="radio"/>, 则v-model收 集的是 value 值，且要给标签配置 value 值  
若：<input type=" checkbox"/>  
    1.没有配置 input 的 value 属性，那么收集的就是 checked(勾选or未勾选，是布尔值)  
    2.配置 input 的 value 属性：  
        (1) v-model 的初始值是非数组，那么收集的就是checked (勾选or未勾选，是布尔值)  
        (2) v -model 的初始值是数组，那么收集的的就是 value 组成的数组  
```

#### v-model 的三个 修饰符

* lazy：失去焦点再收集数据
* number：输入字符串转为有效的数字
* trim：输入首尾空格过滤

## 实例

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
        <form @submit.prevent="demo">
            <label for="demo">账号：</label>
            <!--.trim 做为数字来收集-->
            <input type="text" id="demo" v-model.trim="account"><br /><br />
            密码：<input type="password" v-model="password"><br /><br />
            <!--.number 做为数字来收集-->
            年龄：<input type="number" v-model.number="age"><br /><br />
            性别：
            男<input type="radio" name="sex" v-model="sex" value="male">
            女<input type="radio" name="sex" v-model="sex" value="female"><br /><br />
            爱好：
            吃饭<input type="checkbox" v-model="hobby" value="eat">
            睡觉<input type="checkbox" v-model="hobby" value="sleep">
            打游戏<input type="checkbox" v-model="hobby" value="game"><br /><br />
            所属地区：
            <select v-model="city">
                <option value="">请选择地区</option>
                <option value="beijing">北京</option>
                <option value="shanghai">上海</option>
                <option value="guangzhou">广州</option>
            </select><br /><br />
            其他信息：
            <!--.lazy 在失去焦点时再收集信息-->
            <textarea v-model.lazy="other"></textarea><br /><br />
            <input type="checkbox" v-model="agree">阅读并接受<a href="https://www.baidu.com"
                rel="external nofollow">《用户协议》</a><br /><br />
            <button>提交</button>
        </form>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false
        //创建vue实例
        new Vue({
            el: "#root",
            data: {
                account: "",
                password: "",
                age: 18,
                sex: "male",
                hobby: [], //多选试需要设置成数组
                city: "",
                other: "",
                agree: "" //只判断是否打钩，字符
            }
        })
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
