---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_列表过滤和排序(10)"
date: 2022-06-08T23:55:49+08:00
tags: [
    "vue",
    "sort",
    "filter",
]
categories: [
    "vue",
]
---

这一篇整理了列表过滤和排序相关的内容。  

## 实例

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
        <input type="text" placeholder="请输入名字" v-model="keyword">
        <button @click="sortType=2">年龄升序</button>
        <button @click="sortType=1">年龄降序</button>
        <button @click="sortType=0">原顺序</button>
        <ul>
            <li v-for="(p,index) of filPersons" :key="p.id">
                {{p.name}}-{{p.age}}-{{p.sex}}
            </li>
        </ul>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                keyword: '',
                sortType: 0, // 0 原顺序 1 降序 2 升序
                persons: [
                    { id: 1, name: '马冬梅', age: 18, sex: '女' },
                    { id: 2, name: '周冬雨', age: 15, sex: '女' },
                    { id: 3, name: '周杰伦', age: 12, sex: '男' },
                    { id: 4, name: '温兆伦', age: 23, sex: '男' }
                ],
                filPersons_watch: []
            },
            // watch: {
            //     keyword: {
            //         immediate: true,
            //         handler(val) {
            //             this.filPersons = this.persons.filter((p) => {
            //                 return p.name.includes(val);
            //             })
            //         }
            //     }
            // },
            // computed:{
            //     filPersons(){
            //         return this.persons.filter((p) => {
            //             return p.name.includes(this.keyword);
            //         })
            //     }
            // }
            computed: {
                filPersons() {
                    let arr = this.persons.filter((p) => {
                        return p.name.includes(this.keyword);
                    });
                    if (this.sortType) {
                        arr.sort((a, b) => {
                            return this.sortType === 1 ? b.age - a.age : a.age - b.age;
                        })
                    } 
                    return arr;
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
