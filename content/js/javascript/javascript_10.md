---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_Dom(10)"
date: 2022-05-08T22:25:49+08:00
tags: [
    "js",
    "dom",
    "document",
    "node",
    "javascript",
]
categories: [
    "JavaScript",
]
---

这里整理了DOM相关内容。<!--more-->

## 1.DOM

DOM, 全称 Document Object Model 文档 对象 模型, 是一个网页的树形结构。  
下图是其结构:  
[图片备用地址](https://limingxie.github.io/images/javascript/pic_htmltree.gif)  
![java_compile_execut](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/javascript/pic_htmltree.gif)

#### 节点Node

常用Node类型:

* **文档节点:** 整个HTML文档
* **元素节点:** HTML文档中的HTML标签
* **属性节点:** 元素的属性
* **文本节点:** HTML标签中的文本内容

[图片备用地址](https://limingxie.github.io/images/javascript/html_node.png)  
![java_compile_execut](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/javascript/html_node.png?x-oss-process=image/resize,w_600,m_lfit)

## 2.例子

DOM的操作网上有很多资料，  
我的目的也是为了学习NodeJs打个基础，所以这里就快速略过了，  
自己简单的做了2个例子，仅供参考。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <style type="text/css">
        #div {
            width: 200px;
            height: 100px;
            background-color: #f00;
        }

        #box {
            width: 30px;
            height: 30px;
            background-color: blueviolet;
            position: absolute;
        }
    </style>
    <script type="text/javascript">
        console.log("--------------------dom--------------------");
        

        //页面加载以后执行改代码
        window.onload = function () {
            var btn = document.getElementById("btn");
            console.log(btn.innerHTML); //我是一个按钮
            btn.innerHTML = "I'm Button";

            //设置btn的点击事件
            btn.onclick = function () {
                console.log("欢迎点击")
            }

            //getElementById获取元素
            //getElementsByTagName获取标签名获取一组元素节点对象
            //getElementsByName获取name属性获取一组元素节点对象

            var btn01 = document.getElementById("btn01");
            btn01.onclick = function () {
                var name = document.getElementById("name");
                alert("你输入了：" + name.value);
                var bool = confirm("要改按钮名称吗？");
                if(bool){
                    btn01.innerHTML = name.value;
                }
                var bool1 = confirm("添加注释吗？");
                if(bool1){
                    var dd = document.createElement("div");
                    dd.value = name.value;
                    dd.textContent = name.value;
                    document.getElementById("div").appendChild(dd);
                }
            }

            //跟着鼠标跑动的div
            // var box = document.getElementById("box");
            // document.onmousemove = function (event) {
            //     // var left = event.clientX;
            //     // var top =event.clientY;
            //     var left = event.pageX;
            //     var top = event.pageY;

            //     box.style.left = left + "px";
            //     box.style.top = top + "px";
            // }
        };
    </script>

</head>

<body>
    <button id="btn">我是一个按钮</button>
    <div id="div">
        <input type="text" name="name" id="name" value="请输入" />
    </div>
    <button id="btn01">确认</button>
    <div id = "box"> </div>

    <!-- <script type="text/javascript">
        console.log("--------------------dom--------------------");

        var btn = document.getElementById("btn");
        console.log(btn.innerHTML); //我是一个按钮
        btn.innerHTML = "I'm Button";

        //设置btn的点击事件
        btn.onclick = function () {
            console.log("欢迎点击")
        }
    </script> -->
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
