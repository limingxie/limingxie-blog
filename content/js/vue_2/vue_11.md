---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_数据检测(11)"
date: 2022-06-09T23:55:49+08:00
tags: [
    "vue",
    "set",
]
categories: [
    "vue",
]
---

这一篇整理了数据监测相关的内容。  <!--more-->

## Vue监视数据的原理

#### 1.vue会监视data中所有层次的数据

#### 2.如何监测对象中的数据？

通过setter实现监视，且要在new Vue时就传入要监测的数据。
(1).对象中后追加的属性，Vue默认不做响应式处理
(2).如需给后添加的属性做响应式，请使用如下API：
    Vue.set(target，propertyName/index，value) 或
    vm.$set(target，propertyName/index，value)

#### 3.如何监测数组中的数据？

通过包裹数组更新元素的方法实现，本质就是做了两件事：
(1).调用原生对应的方法对数组进行更新。
(2).重新解析模板，进而更新页面。

#### 4.在Vue修改数组中的某个元素一定要用如下方法

1.使用这些API:push()、pop()、shift()、unshift()、splice()、sort()、reverse()
2.Vue.set() 或 vm.$set()

#### 特别注意：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象(vm._data) 添加属性

## 实例1

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
        <h3>人员列表</h3>
        <button @click="updatemadongmei">更新马冬梅</button>
        <ul>
            <li v-for="(p,index) of persons" :key="p.id">
                {{p.name}}-{{p.age}}-{{p.sex}}
            </li>
        </ul>

        <hr />
        <h3>学校：{{name}}</h3>
        <h3>地址：{{address}}</h3>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#app',
            data: {
                name: "小明",
                address: "北京",
                persons: [
                    { id: 1, name: '马冬梅', age: 18, sex: '女' },
                    { id: 2, name: '周冬雨', age: 15, sex: '女' },
                    { id: 3, name: '周杰伦', age: 12, sex: '男' },
                    { id: 4, name: '温兆伦', age: 23, sex: '男' }
                ],
            },
            methods: {
                updatemadongmei() {
                    // this.persons[0].name='马老师'; 可以
                    // this.age = 50; 可以
                    // this.sex = '男'; 可以
                    //因为直接给数组的索引该内容，所以不奏效
                    // this.persons[0] = { id: 1, name: '马老师', age: 50, sex: '男' }
                    this.persons.splice(0,1,{ id: 1, name: '马老师', age: 50, sex: '男' });
                }
            }
        });

        //添加data属性,这么写报错, 不能在跟数据属性里添加属性。
        // Vue.set(vm._data, "age", 18);
        // vm.$set(vm, sex, '男');
        //只能写在vm的sub属性里添加
        Vue.set(vm._data.students, "age", 18);
        vm.$set(vm.students, sex, '男');
    </script>
</body>

</html>
```

## 实例2

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script src="../js/vue.js" type="text/javascript" charset="uft-8"></script>
</head>

<body>
    <script type="text/javascript">
        let data = {
            name: '小明',
            age: 18
        }

        const obs = Observer(data);
        console.log(obs);

        let vm = {}
        vm._data = data = obs

        function Observer(obj) {
            //汇总对象中的所有的属性，形成一个数组
            const keys = Object.keys(obj);
            //遍历
            keys.forEach((k) => {
                Object.defineProperty(this, k, {
                    get() {
                        return obj[k];
                    },
                    set(val) {
                        console.log(`${k}被改了，去解析摩玛，生成新的虚拟DOM等等...`);
                        obj[k] = val;
                    }
                })
            })
        }

    </script>
</body>

</html>
```

## 实例3

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
        <h3>学生信息</h3>

        <button @click="student.age++">年龄+1</button> <br /><br />
        <button @click="addSex">添加性别属性，默认值：男</button> <br /><br />
        <button @click="addFriend">在列表首位添加一个朋友</button> <br /><br />
        <button @click="updateFirstFriendName">修改第一个朋友的名字为:张三</button> <br /><br />
        <button @click="addHobby">添加一个爱好</button> <br /><br />
        <button @click="updateFirstHobby">修改一个爱好为：开车</button> <br /><br />
        <button @click="removeSmoke">过滤一个爱好为：抽烟</button> <br /><br />

        <h4>姓名:{{student.name}}</h4>
        <h4>姓名:{{student.age}}</h4>
        <h4 v-show="student.sex">性别:{{student.sex}}</h4>
        <h4>爱好:</h4>

        <ul>
            <li v-for="(h,index) in student.hobby" :key="index">
                {{h}}
            </li>
        </ul>

        <h4>朋友们:</h4>
        <ul>
            <li v-for="(f,index) in student.friends" :key="index">
                {{f.name}}-{{f.age}}
            </li>
        </ul>

    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                student: {
                    name: 'tom',
                    age: 18,
                    hobby: [
                        '篮球',
                        '足球',
                        '游泳'
                    ],
                    friends: [
                        { name: '小明', age: 18 },
                        { name: '小红', age: 19 },
                        { name: '小白', age: 20 }
                    ]
                },
            },
            methods: {
                addSex() {
                    this.$set(this.student, 'sex', '男')
                },
                addFriend() {
                    this.student.friends.unshift({ name: '小张', age: 17 });
                },
                updateFirstFriendName() {
                    this.student.friends[0].name = '张三';
                },
                addHobby() {
                    this.student.hobby.push('长跑');
                },
                updateFirstHobby() {
                    this.student.hobby.splice(0, 1, '开车');
                    // Vue.set(this.student.hobby,0,'开车');
                },
                removeSmoke() {
                    this.student.hobby = this.student.hobby.filter(h => h !== '抽烟');
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
