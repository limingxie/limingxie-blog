---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_数组(8)"
date: 2022-05-08T01:55:49+08:00
tags: [
    "js",
    "javascript",
]
categories: [
    "JavaScript",
]
---

这里整理了数组的适用方法。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript">
        console.log("--------------------数组Array--------------------");

        //数组的创建
        var arr = new Array();
        console.log(typeof arr); //object
        arr[0] = 0;
        arr[1] = 1;
        arr[2] = 2;
        console.log(arr);       //(3) [0, 1, 2]
        console.log(arr[2]);    //2
        console.log(arr[3]);    //undefined

        //[]创建数组
        var arr1 = [1, 2, 3, 4, 5];
        console.log(typeof arr1);   //object
        console.log(arr1);          //(5) [1, 2, 3, 4, 5]  
        console.log(arr1.length);   //5

        //指定索引赋值后的数组长度
        arr1[10] = 10;
        console.log(arr1.length);   //11
        console.log(arr1);          //(11) [1, 2, 3, 4, 5, , , , , , 10]

        //可以修改数组长度
        arr1.length = 10
        console.log(arr1.length);   //10
        console.log(arr1);          //(10) [1, 2, 3, 4, 5, , , , , ,]

        arr1.length = 2
        console.log(arr1);          //(2) [1, 2]

        //给数组的后一个索引赋值
        arr[arr.length] = 3;
        arr[arr.length] = 4;
        console.log(arr.length);    //5
        console.log(arr);           //(5) [0, 1, 2, 3, 4]

        //数组可以加属性
        var arr2 = new Array();

        arr2[0] = 1;
        arr2.name = "张三";

        console.log(arr2);      //(1) [1] {name: '张三'}

        //new Array 和 [] 区别
        arr = new Array(10, 20, 30, 40, 50);
        console.log(arr);       //(5) [10, 20, 30, 40, 50]

        //new
        arr = new Array(10);
        console.log(arr);       //(10) [undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined]
        console.log(arr.length); //10

        //[]
        arr = [10];
        console.log(arr);           //(1) [10]
        console.log(arr.length);    //1

        //数组可以付不同类型的值，包括对象和数组
        arr = [1, "阿", true, null, undefined, { name: "张三" }, [1, 2, 3]];
        console.log(arr);           //(7) [1, '阿', true, null, undefined, {…}, [1, 2, 3]]

        arr = [{ name: "张三" }, function () { console.log("我是函数") }];
        console.log(arr);           //(2) [{…}, function () {…}]
        console.log(arr[0].name);   //张三
        console.log(arr[1]());      //我是函数

        //push: 给数组的后面添加元素
        var arr3 = ["张一", "张二", "张三"];
        var arr4 = ["李四", "王五", "赵六"];

        arr3.push(arr4, "张七");
        console.log(arr3);      //(7) ["张一", "张二", "张三", ["李四", "王五", "赵六"], "张七"]

        arr3 = ["张一", "张二", "张三"];
        var result = arr3.push("李四", "王五", "赵六");
        console.log(arr3);          //(6) ["张一", "张二", "张三", "李四", "王五", "赵六"]
        console.log(result);       //6   返回长度

        //pop: 删除最后一个元素，并返回该元素
        var arr4 = ["张一", "张二", "张三", "李四", "王五", "赵六"];
        var result = arr4.pop();
        console.log(arr4);      //(5) ["张一", "张二", "张三", "李四", "王五"]
        console.log(result);    //赵六

        //unshift: 给数组的前面添加元素,并返回数组长度
        var arr5 = ["张一", "张二", "张三", "李四", "王五", "赵六"];
        result = arr5.unshift("阿", "阿阿");
        console.log(arr5);      //(8) ["阿", "阿阿", "张一", "张二", "张三", "李四", "王五", "赵六"]
        console.log(result);    //8

        //shift: 删除数组的第一个元素，并返回该元素
        var arr6 = ["张一", "张二", "张三", "李四", "王五", "赵六"];
        result = arr6.shift();
        console.log(arr6);      //(5) ["张二", "张三", "李四", "王五", "赵六"]
        console.log(result);    //"张一"

        //array 的遍历
        for (let i = 0; i < arr6.length; i++) {
            console.log(arr6[i]);
        }

        //forEach IE8以上才能支持
        arr6.forEach(function (value, index, array) {
            console.log(value);
            console.log(index);
            console.log(array);
        })

        //slice(start, end) 获取选定元素
        var arr7 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
        result = arr7.slice(1, 4);
        console.log(result);      //(3) [2, 3, 4]

        result = arr7.slice(2);
        console.log(result);      //(8) [3, 4, 5, 6, 7, 8, 9, 10]

        result = arr7.slice(2, -2); //从2开始倒数第2个为止
        console.log(result);      //(6) [3, 4, 5, 6, 7, 8]

        //splice(start, count) 
        var arr8 = [1, 2, 3, 4, 5, 6, 7];
        result = arr8.splice(2, 3);
        console.log(result);      //(3) [3, 4, 5]
        console.log(arr8);        //(4) [1, 2, 6, 7]

        result = arr8.splice(0, 3);
        console.log(result);      //(3) [1, 2, 6]

        var arr9 = [1, 2, 3, 4, 5, 6, 7];
        result = arr9.splice(0, 3, "哈哈", "嘿嘿");
        console.log(result);      //(3) [1, 2, 3]
        console.log(arr9);    //(6) ['哈哈', '嘿嘿', 4, 5, 6, 7]

        arr9.splice(1, 0, "ok")
        console.log(arr9);    //(7) ['哈哈', 'ok', '嘿嘿', 4, 5, 6, 7]

        //concat() 合并两个数组, 
        var arr11 = ["张一", "张二", "张三"];
        var arr12 = ["李四", "王五", "赵六"];

        var result = arr11.concat(arr12, "哈哈", "嘿嘿");
        console.log(result);     //(8) ['张一', '张二', '张三', '李四', '王五', '赵六', '哈哈', '嘿嘿']

        //join(分隔符)
        var result = arr11.join("|");
        console.log(result);      //张一|张二|张三

        //reverse() 反转数组
        var arr13 = ["张一", "张二", "张三", "李四", "王五", "赵六"];
        result = arr13.reverse();
        console.log(arr13);     //(6) ["赵六", "王五", "李四", "张三", "张二", "张一"]
        console.log(result);    //(6) ["赵六", "王五", "李四", "张三", "张二", "张一"]

        //sort()
        var arr14 = [5, 3, 7, 1, 9, 2, 4, 6, 8];
        arr14.sort();
        console.log(arr14); //(9) [1, 2, 3, 4, 5, 6, 7, 8, 9]

        var arr15 = [15, 3, 7, 1, 9, 2, 4, 6, 8];
        arr15.sort();
        console.log(arr15); //(9) [1, 15, 2, 3, 4, 6, 7, 8, 9]

        arr15.sort(function(a,b){
            return a - b;
        });

        console.log(arr15); //(9) [1, 2, 3, 4, 6, 7, 8, 9, 15]

        //倒叙排序
        arr15.reverse();
        console.log(arr15); //(9) [15, 9, 8, 7, 6, 5, 4, 3, 2]

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
