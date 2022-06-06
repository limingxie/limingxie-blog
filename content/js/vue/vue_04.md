---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_事件(04)"
date: 2022-06-02T23:55:49+08:00
tags: [
    "vue",
    "event",
]
categories: [
    "vue",
]
---

## 1.事件处理

1. 使用v-on:xxxx 简写 @xxx 绑定事件
2. 时间回调写在Vue实例的 methods 对象中, 不要写在data中
3. methods中配置的函数，不要用箭头函数，这样this就不指向Vue实例了
4. 函数的参数中 event 可以用 $event 指定。

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
        <h3>你好，{{name}}</h3>
        <button v-on:click="showIfo11">点我11(不传参)</button>
        <!-- 简写可以用 @click -->
        <button @click="showIfo22($event,66)">点我22(传参)</button>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                name: '小明' //这里的是数据劫持和数据代理
                //函数不要写在这里，可以用但是不推荐（函数没必要做数据代理）
            },
            methods: {
                showIfo11(event) {//不能用箭头函数
                    //console.log(event.target.innerText);
                    //console.log(this) // 此处的this是 Vue实例
                    alert('你好11，' + this.name);
                },
                showIfo22(event,number) {
                    console.log(event, number)
                    alert('你好22，' + this.name);
                }
            }
        });
    </script>
</body>

</html>
```

## 2.键盘修饰符

**prevent**: 阻止默认事件  

**stop**: 阻止时间冒泡  

**once**: 事件只触发一次  

**capture**: 使用事件的捕获模式  

**self**: 只有event.target是当前操作的元素时才触发事件  

**passive**: 事件的默认行为立即执行，无需等待事件回调执行完毕  

#### 实例

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
    <style>
        * {
            margin-top: 10px;
        }

        .demo1 {
            height: 50px;
            background-color: skyblue;
        }

        .box1 {
            padding: 5px;
            background-color: skyblue;
        }

        .box2 {
            padding: 5px;
            background-color: orange;
        }

        .list{
            width: 200px;
            height: 200px;
            background-color: plum;
            overflow: auto;
        }
        li{
            height: 100px;
        }
    </style>
</head>

<body>
    <div id="app">
        <h3>你好，{{name}}</h3>
        <!-- 阻止默认事件 -->
        <a href="https://www.baidu.com" @click.prevent="showInfo">点我提示信息</a>
        <!-- <a href="https://www.baidu.com" @click.stop.prevent="showInfo">点我提示信息</a> -->

        <!-- 阻止冒泡 -->
        <div class="demo1" @click="showInfo">
            <button @click.stop="showInfo">点我提示信息</button>
        </div>

        <!-- 事件只触发一次 -->
        <button @click.once="showInfo">点我提示信息</button>

        <!-- 使用事件的捕获模式 -->
        <div class="box1" @click.capture="showMessage(1)">
            div1
            <div class="box2" @click="showMessage(2)">
                div2
            </div>
        </div>

        <!-- 只有event.target是当前操作的元素时才触发事件 -->
        <div class="demo1" @click.self="showInfo">
            <button @click="showInfo">点我提示信息</button>
        </div>

        <!-- 事件的默认行为立即执行，无需等待事件回调执行完毕 -->
        <!-- scroll 滚动条  wheel 鼠标滚轮 -->
        <ul @wheel.passive="demo" class="list">
            <li>1</li>
            <li>2</li>
            <li>3</li>
            <li>4</li>
        </ul>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                name: '小明'
            },
            methods: {
                showInfo(e) {
                    // e.preventDefault();
                    // e.stopPropagation();
                    alert('你好11，' + this.name);
                },
                showMessage(e) {
                    // e.preventDefault();
                    // e.stopPropagation();
                    alert('你好11，' + this.name);
                },
                demo(){
                    for (let i = 0; i < 100000; i++) {
                        console.log("#");
                    }
                    console.log("终于结束了")
                }
            }
        });
    </script>
</body>

</html>
```

## 3.键盘事件

#### 1.常用按键事件别名

    回车 => enter
    删除 => delete
    退出 => esc
    空格 => space
    换行 => tab     像tab键只能用 keydwon 因为up时直接切换到下一个元素
    上 => up
    下 => down
    左 => left
    右 => right

#### 2.Vue未提供的别名，只能用按键原始的key值绑定

比如 Enter, 驼峰式需要用 - 分割 caps-lock

#### 3.特殊键：=> ctrl  alt  shift meta

是系统修饰键比较特殊：  
1）keyup：按下修饰键同时，再按下其他键，随后释放其他键，事件才被触发  
2) keydown：正常触发事件  

#### 4.可以定义一个别名，Vue.config.keyCodes.xxx = 键码 但是也不是那么推荐

可以定义一个别名，Vue.config.keyCodes.xxx = 键码
但是也不是那么推荐。

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
        <h3>你好，{{name}}</h3>
        <input type="text" placeholder="按键回车提示" @keyup.enter="showInfo">
        <!-- <input type="text" placeholder="安县回车提示" @keyup.Enter="showInfo"> -->
        <!-- <input type="text" placeholder="安县回车提示" @keyup.ctrl.y="showInfo"> -->
        <!-- <input type="text" placeholder="安县回车提示" @keyup.13="showInfo"> 不推荐，以后会消除 -->
        <!-- <input type="text" placeholder="安县回车提示" @keyup.caps-lock="showInfo"> -->
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false;
        Vue.config.keyCodes.huiche = 13;
        new Vue({
            el: '#app',
            data: {
                name: '小明' 
            },
            methods: {
                showInfo(e) {
                    // if(e.keyCode != 13) {
                    //     return
                    // }
                    console.log(e.key, e.keyCode);
                    console.log(e.target.value);
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
