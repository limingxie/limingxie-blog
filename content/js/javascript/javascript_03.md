---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_流程控制(3)"
date: 2022-05-06T09:05:49+08:00
tags: [
    "js",
    "javascript",
    "if",
    "else",
    "switch",
    "case",
    "when",
    "break",
    "continue",
    "do",
    "while",
    "for",
]
categories: [
    "JavaScript",
]
---

这一遍整理了 if, switch, while, for 的语法。  
因为和其他语言没什么区别，所以这里就简单的整理了一下使用方法。

## if, switch, while, for

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">
        console.log("--------------------if--------------------");

        var aa = prompt("请输入分数");
        if (aa <= 100 && aa >= 60) {
            console.log("分数及格");
        } else if (aa < 60 && aa >= 0) {
            console.log("分数不及格");
        } else {
            console.log("分数不合法");
        }

        console.log("--------------------switch--------------------");

        var bb = prompt("请输入分数");

        switch (parseInt(bb / 10)) {
            case 10:
            case 9:
            case 8:
            case 7:
            case 6:
                console.log("分数及格");
                break;
            case 5:
            case 4:
            case 3:
            case 2:
            case 1:
            case 0:
                console.log("分数不及格");
                break;
            default:
                console.log("分数不合法");
                break;
        }

        switch (true) {
            case bb <= 100 && bb >= 60:
                console.log("分数及格");
                break;
            case bb < 60 && bb >= 0:
                console.log("分数不及格");
            default:
                console.log("分数不合法");
                break;
        }

        console.log("--------------------while--------------------");

        var i = 0;
        //先判断后，执行循环体
        while (i < 10) {
            console.log(i);
            i++;
        }

        var j = 0;
        //执行循环体后，再判断
        do {
            console.log(j);
            j++;
        } while (i < 10);

        var k = 0;
        //当while条件为true的时候，循环体必须要有退出条件
        while (true) {
            console.log(k);
            k++;
            //退出循环条件
            if (k > 10) {
                break;
            }
        }

        console.log("--------------------for--------------------");

        for (let i = 0; i < 10; i++) {
            console.log(i);
        }

        //这是死循环
        /*
            for (; ;) {
                console.log("死循环");
            }
        */

        outer:
        for (let i = 0; i < 10; i++) {
            for (let j = 0; j < 10; j++) {
                console.log("i=" + i + " | " + " j=" + j);
                if (i >= 2 && j >= 2) {
                    break outer;
                }   //这是退出到outer循环的条件，不指名会退出这个循环
            }
        }

        outer:
        for (let i = 0; i < 10; i++) {
            for (let j = 0; j < 10; j++) {
                if (i == 2) {
                    continue outer;
                }   //这是返回到outer循环的条件，不指名会返回出这个循环
                console.log("i=" + i + " | " + " j=" + j);
            }
        }

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
