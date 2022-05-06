---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_Object对象(4)"
date: 2022-05-06T10:05:49+08:00
tags: [
    "js",
    "javascript",
    "object",
]
categories: [
    "JavaScript",
]
---

## 1.对象分类

对象可以分3种。  

**内建对象:** 有ES标准中定义的对象。比如：String, Number, Object....  
**宿主对象:** 有JS运行环境提供的对象。主要是游览器提供的对象。比如：BOM, DMO, window, global, self, this ...  
**自定义对象:** 有开发人员自己创造的对象。

整理先整理如何使用**自定义对象**使用方式。  

## 2.代码实例

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>

    <script type="text/javascript">

        console.log("--------------------object--------------------");
        //写new就代表调用构造函数constructor
        var obj = new Object();
        var obj1 = {};

        //创建时直接添加属性。属性也可以加object
        var obj2 = {
            name: "张三",
            age: 18,
            userName: "zhang.shan",
            city: {
                code: "BJ",
                name: "北京"
            }
        };

        //添加属性
        obj.name = "张三";
        //可以使用[]添加属性
        obj["age"] = 12;
        obj.userName = "zhang.shan";

        console.log("obj type:", typeof obj); //obj type: object
        console.log("obj:", obj);           //obj  { age: 18, name: '张三', userName: 'zhang.shan' }
        console.log("obj2 type:", typeof obj2); //obj2 type: object
        console.log("obj2:", obj2);         //obj2  { age: 18, city: {code: 'BJ', name: '北京'}, name: '张三', userName: 'zhang.shan' }

        //删除属性
        delete obj.userName;
        console.log("obj:", obj);   //obj  { age: 18, name: '张三' }
        console.log("obj.userName:", obj.userName);   //obj.userName: undefined

        //属性名可以用中文，但是建议别用
        obj1.名字 = "张三";
        obj1.年龄 = 18;
        console.log("obj1:", obj1);   //obj  { 名字: '张三', 年龄: 18 }

        //使用[]添加属性的灵活用法。123这种特殊属性直接 obj.123会报错，但是可以用 obj["123"] = “哈哈"
        obj["123"] = "哈哈";

        var n = "456";
        obj[n] = "嘿嘿";
        console.log("obj:", obj);   //obj  { age: 18, name: '张三', 123: '哈哈'， 456: '嘿嘿' }

        //修改属性内容
        obj[n] = "别嘿嘿";
        console.log("obj:", obj);   //obj  { age: 18, name: '张三', 123: '哈哈'， 456: '别嘿嘿' }

        //新添加属性
        n = "haha";
        obj[n] = "怎么又来了个haha";
        console.log("obj:", obj);   //obj  { age: 18, name: '张三', 123: '哈哈'， 456: '别嘿嘿', haha: '怎么又来了个haha' }

        //判断属性是否存在
        console.log("name" in obj);   //true
        console.log("userName" in obj);   //false

        //引用类型测试
        var obj3 = { name: "张三", age: 18 };
        var obj4 = obj3;
        console.log("obj3:", obj3);   //obj3  { name: '张三', age: 18 }
        console.log("obj4:", obj4);   //obj4  { name: '张三', age: 18 }

        obj3.name = "李四";
        console.log("obj3:", obj3);   //obj3  { name: '李四', age: 18 }
        console.log("obj4:", obj4);   //obj4  { name: '李四', age: 18 }

        //赋null不会影响其他对象
        obj3 = null;
        console.log("obj3:", obj3);   //obj3  null
        console.log("obj4:", obj4);   //obj4  { name: '李四', age: 18 }

        var city1 = {code: "BJ", name: "北京"};
        obj4.city = city1;
        console.log("obj4:", obj4);   //obj4  { name: '李四', age: 18, city: {code: 'BJ', name: '北京'} }

        //深拷贝, obj4拷贝的是city的堆内存地址，而不是city的值
        city1 = null;
        console.log("obj4:", obj4);   //obj4  { name: '李四', age: 18, city: {code: 'BJ', name: '北京'} }

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
