---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_过滤器(13)"
date: 2022-06-11T23:55:49+08:00
tags: [
    "vue",
    "filters",
    "filter",
]
categories: [
    "vue",
]
---

这一篇整理了过滤器相关的内容。  

#### 过滤器的定义

对要显示的数据进行特定格式化后再显示(适用于一些简单逻辑的处理）

#### 语法

1.注册过滤器：​​​Vue.filter(name,callback)​​​或​​new Vue{filters:{}}​​​
2.使用过滤器：​​{{ xxx│过滤器名}}​​或​​v-bind:属性= “xxx│过滤器名"​​

#### 备注

1.过滤器也可以接收额外参数、多个过滤器也可以串联
2.并没有改变原本的数据，是产生新的对应的数据效的数字

#### 实例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Vue初识</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script type="text/javascript" src="../js/dayjs.min.js"></script>
</head>
<body>
    <div id="root">
        <!--计算属性实现-->
        <h2>现在时间是：{{fmtTime}}</h2>
        <!--methods实现-->
        <h2>现在时间是：{{getFmtTime()}}</h2>
        <!--过滤器实现(传参)-->
        <h2>现在时间是1：{{time | timeFormater('YYYY年MM月DD日 HH:mm:ss') | mySlice1}}</h2>
        <h2>现在时间是：{{time | timeFormater('YYYY年MM月DD日 HH:mm:ss') | mySlice}}</h2>
    </div>
    <div id="root2">
        <h2>{{msg | mySlice}}</h2>
        <h2 :x="msg | mySlice">Hello world</h2>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false
        //全局过滤器
        Vue.filter('mySlice', function (value) {
            return value.slice(0, 5)
        })
        //创建vue实例
        new Vue({
            el: "#root",
            data: {
                time: Date.now()
            },
            computed: {
                fmtTime() {
                    return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
                }
            },
            methods: {
                getFmtTime() {
                    return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
                }
            },
            //局部过滤器
            filters: {
                timeFormater(value, str = 'YYYY-MM-DD HH:mm:ss') {
                    return dayjs(value).format(str)
                },
                mySlice1(value) {
                    return value.slice(0, 5)
                }
            }
        })

        new Vue({
            el: "#root2",
            data: {
                msg: "hello world"
            }
        })
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
