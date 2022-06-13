---
author: "li_mingxie"
title: "【Vue笔记】Vue入门简介_游览器本地存储(22)"
date: 2022-06-20T23:55:49+08:00
tags: [
    "vue",
]
categories: [
    "vue",
]
---

#### WebStorage  

1.存储内容大小一般支持5MB左右（不同浏览器可能还不一样）  
2.浏览器端通过Window.sessionStorage和Window.localStorage属性来实现本地存储机制  
3.相关API：  

* xxxxxStorage.setItem(‘key’,‘value’)  
该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值  
* xxxxxStorage.getItem(‘person’)  
该方法接受一个键名作为参数，返回键名对应的值  
* xxxxxStorage.removeItem  
该方法接受一个键名作为参数，并把该键名从存储中删除  
* xxxxxStorage.clear()  
该方法会i清空存储中的所有数据  

4.备注：  
（1）SessionStorage存储的内容会随着浏览器窗口关闭而小时。  
（2）LocalStorage存储的内容，需要手动清除才会消失。  
（3）xxxxxStorage.getItem(xxx)如果xxx对应的value获取不到，那么getItem的返回值是null  
（4）JSON.parse(null)的结果依然是null  

#### 补充  

1. JSON.stringify() 方法用于将 JavaScript 值转换为 JSON 字符串。  
2. JSON.parse() 方法将数据转换为 JavaScript 对象  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
