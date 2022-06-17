---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_路由守卫(36)"
date: 2022-07-04T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

这一篇整理了路由相关的内容。  <!--more-->

1.路由作用：对路由进行权限控制
2.路由分类：全局守卫、独享守卫、组件内守卫

## 路由前置-路由守卫

现在的需求是访问 News 和 Message 时验证 localstorage，如果 school 的值是 三里屯小学 才能正常访问，否则提示没有权限：
增加 localstorage：就能正常访问
修改 index.js ，给每个路由增加 name 属性，然后增加前置路由守卫

```js
......
//创建一个路由器
const router = new VueRouter({
    routes: [
        {
            name: 'guanyu',
            path: '/about',
            component: About
        },
        {
            name: 'zhuye',
            path: '/home',
            component: Home,
            children: [
                {
                    path: 'news',
                    component: News
                },
                {
                    name: 'xiaoxi',
                    path: 'message',
                    component: Message,
                    children: [
                        {
                            name: 'xiangqing',
                            path: 'detail',
                            component: Detail,

                            props({query: {id, title}}) {
                                return {id, title}
                            }

                        }
                    ]
                }
            ]
        },
    ]
})
//全局前置路由守卫
//初始化的时候调用、每次切换之前调用
router.beforeEach((to, from, next) => {
    console.log(to, from);
    if (to.path === '/home/news' || to.name ==='xiaoxi') {
        if (localStorage.getItem("school") === "三里屯小学") {
            next()
        }else {
            alert("无权限")
        }
    }else{
        next()
    }
})
export default router;
```

可以看到我们在前置路由守卫中判断了当路径是 news 或 name =‘xiaoxi’ 时进行权限的校验，如果判断条件太多，这个 if 语句就会很繁琐，我们可以在配置路由信息时使用 ​​meta​​

```js
......
const router = new VueRouter({
    routes: [
        {......},
        {
            ......
            children: [
                {
                    path: 'news',
                    ......
                    meta:{
                        isAuth:true
                    }
                },
                {
                    name: 'xiaoxi',
                    ......
                    meta:{
                        isAuth:true
                    },
                    children: [......]
                }
            ]
        },
    ]
})
//全局前置路由守卫
//初始化的时候调用、每次切换之前调用
router.beforeEach((to, from, next) => {
    if (to.meta.isAuth) {
        ......
    }else{
        ......
    }
})
export default router;
```

## 全局后置-路由守卫

现在的需求是点击链接，页签的标题也进行修改  

首先需要给每个路由设置 ​​​meta​​​ 增加一个字段标识标题，然后使用后置路由守卫 ​​afterEach​​ 来修改  

```js
const router = new VueRouter({
    routes: [
        {
            name: 'guanyu',
            ......
            meta: {title: '关于'}
        },
        {
            name: 'zhuye',
            ......
            meta: {title: '主页'},
            children: [
                {
                    path: 'news',
                    component: News,
                    meta: {
                        isAuth: true,
                        title: '新闻'
                    }
                },
                {
                    name: 'xiaoxi',
                    path: 'message',
                    component: Message,
                    meta: {
                        isAuth: true,
                        title: '消息'
                    },
                    children: [
                        {
                            name: 'xiangqing',
                            path: 'detail',
                            component: Detail,
                            meta: {
                                title: '详情'
                            },
                            ......
                        }
                    ]
                }
            ]
        },
    ]
})
......
//全局后置路由
//初始化的时候被调用、每次路由切换之后被调用
router.afterEach((to, from) => {
    console.log(to, from);
    document.title = to.meta.title || ""
})
export default router;

```

## 独享路由守卫  

独享路由守卫即给路由单独设置的路由守卫，使用 ​​beforeEnter​​，例如我们单独给消息设置一下当 localstorage 中 school 为三里屯小学时才能查看，否则就提示，功能和之前写的一样  

```js
{
      name: 'xiaoxi',
      path: 'message',
      component: Message,
      meta: {
          isAuth: true,
          title: '消息'
      },
      beforeEnter: ((to, from, next) => {
              if (to.meta.isAuth) {
                  if (localStorage.getItem("school") === "三里屯小学") {
                      next()
                  } else {
                      alert("无权限")
                  }
              } else {
                  next()
              }
          }
      ),
      children: []
  }
```

## 组件内路由守卫  

给组件内单独设置路由守卫，例如修改 About  

```js
<template>
  <h2>我是About的内容</h2>
</template>

<script>
export default {
  name: "About",
  //通过路由规则，进入该组件时被调用
  beforeRouteEnter(to,from,next){
    console.log("About beforeRouteEnter",to,from);
    //一些组件内特有的
  },
  //通过路由规则，离开该组件时被调用
  beforeRouteLeave(to,from,next){
    console.log("About beforeRouteLeave",to,from);
    //一些组件内特有的
    next()
  }
}
</script>
```

## 路由器的两种工作模式  

1、对于一个url来说，什么是hash值?  
​​​#​​​及其后面的内容就是hash值  
2、hash值不会包含在HTTP请求中，即: hash值不会带给服务器  
3.、hash模式：  
①地址中永远带着 # 号，不美观。  
②若以后将地址通过第三方手机app分享，若 app 校验严格，则地址会被标记为不合法  
③兼容性较好  
4、history 模式：  
①地址干净，美观  
②兼容性和 hash 模式相比略差  
③应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题  

执行​​npm run build​​​进行打包，目录下会生成一个 dist 文件夹，部署到线上  

修改路由的工作模式，修改 ​​​mode​​​，值为​​history​​​或​​hash​​​  
这里不做过多讲解，可以修改看下路径的区别，或用 express 部署一个本地的服务器把代码部署一下看看效果

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
