---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_key的作用和原理(09)"
date: 2022-06-07T23:05:49+08:00
tags: [
    "vue",
    "key",
    "value",
]
categories: [
    "vue",
]
---

这一篇这里了key的作用和原理。  

## 1.作用和原理

#### 一、虚拟DOM中key的作用

key是虚拟DOM对象的标识，当状态中的数据发生变化时，Vue会跟据【新数据】生成【新的虚拟DOM】，  
随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：

#### 二、对比规则

1. 旧虚拟DOM中找到了与新虚拟DOM相同的key：  
    1）若虚拟DOM中内容没变，直接使用之前的真实DOM。  
    2）若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM。  
2. 旧虚拟DOM中未找到与新虚拟DOM相同的key  
    创建新的真实DOM，随后渲染到页面。  

[图片备用地址](https://limingxie.github.io/images/js/vue/vue_key_01.png)  
![vue_key_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/vue_key_01.png?x-oss-process=image/resize,w_700,m_lfit)

[图片备用地址](https://limingxie.github.io/images/js/vue/vue_key_02.png)  
![vue_key_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/js/vue/vue_key_02.png?x-oss-process=image/resize,w_700,m_lfit)

#### 三、用index作为key可能会引发的问题

1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：  
    会产生没有必要的真实DOM更新==>界面效果没有问题，但效率低。  
2. 如果结构中还包含输入类的DOM：  
    会产生错误DOM更新==>界面有问题  

#### 四、开发中如何选择key？

1. 最好使用每条数据的唯一标识作为key，比如id、手机号、身份证号、学号等唯一值。  
2. 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key是没有问题的。  

## 2.实例

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
        <!-- 使用 v-show 做条件渲染 只是把display = false -->
        <h3 v-show="true">{{name}}</h3>
        <h3 v-show="1===2">{{name}}</h3>

        <!-- v-if 直接把元素去掉 -->
        <h3 v-if="true">{{name}}</h3>
        <h3 v-if="1===2">{{name}}</h3>

        <hr />

        <h3>现在n:{{n}}</h3>
        <button @click="n++">点我+1</button>
        <div v-if="n%3 === 1">小明</div>
        <div v-else-if="n%3 === 2">小红</div>
        <div v-else>小张</div>

        <!-- template v-if 配合使用  v-show是不能跟 template一起用 -->
        <!-- <template v-show="n%3===1"> -->
        <template v-if="n%3===1">
            <h3>小明</h3>
            <h3>小红</h3>
            <h3>小张</h3>
        </template>

    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#app',
            data: {
                name: '张三',
                n:0
            }
        });
        
    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
