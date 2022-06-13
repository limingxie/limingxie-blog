---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_侦听属性(06)"
date: 2022-06-04T23:45:49+08:00
tags: [
    "vue",
    "watch",
]
categories: [
    "vue",
]
---

这一篇整理了侦听属性相关的内容。

#### 侦听属性需要理解的内容

1.监视属性发生变化时会调用侦听函数，并且会做相关处理
2.监视属性必须存在才能监视
3.监视属性可以new Vue时添加，也可以 vm.$watch() 添加
4.Vue中watch默认不监测对象内部指的变化，配置deep:true 才能检测变化
5.Vue实例本身是可以检测属性变化。
6.需要异步实现的时候需要用watch实现，computed实现不了

#### 其他注意事项

被Vue管理的所有函数最好写普通方式，这样this才能找到Vue实例
但是不是Vue所管理的函数，比如setTimeout的回调函数，最好写箭头函数，这样this才能找到Vue实例

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
        <h3>今天天气{{info}}</h3>
        <button @click="changeWeather">切换天气</button>
        <!-- 如下可以写简单的语句，但是复杂就不建议这么写 -->
        <!-- <button @click="isHot = !isHot">切换天气</button> -->

        <hr />
        <h3>a的值是:{{numbers.a}}</h3>
        <button @click="numbers.a++">点我让a+1</button>
        <h3>b的值是:{{numbers.b}}</h3>
        <button @click="numbers.b++">点我让b+1</button>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#app',
            data: {
                isHot: true,
                numbers: {
                    a: 1,
                    b: 1
                }
            },
            computed: {
                info: function () {
                    return this.isHot ? '炎热' : '凉爽';
                }
            },
            methods: {
                changeWeather() {
                    this.isHot = !this.isHot;
                }
            },
            watch: {
                isHot: {
                    // immediate:true, //默认是false，初始化时把handler执行一次
                    //当isHot有变化的时候会调用这个函数
                    handler: function (newVal, oldVal) {
                        console.log(newVal, oldVal);
                    }
                },
                //简写
                isHot1(newVal, oldVal) {
                    console.log(newVal, oldVal);
                },
                //计算属性也可以监视
                info: {
                    handler: function (newVal, oldVal) {
                        console.log(newVal, oldVal);
                    }
                },
                //监视多级结构中，某个属性的变化
                'numbers.a': {
                    handler() {
                        console.log('a变了');
                    }
                },
                //监视一个对象的多级数据改变。
                numbers: {
                    deep: true,//默认false, 检测对象属性深度的变化
                    handler() {
                        console.log('numbers变了');
                    }
                }

            }
        });

        //也可以在实例中监视
        /*
            //完整写法
            vm.$watch('info', {
                immediate:true,
                deep: true,
                handler: function (newVal, oldVal) {
                    console.log(newVal, oldVal);
                }
            });
            简写//
            vm.$watch('isHot', function (newVal, oldVal) {
            console.log(newVal, oldVal);
        })
        */
        
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
