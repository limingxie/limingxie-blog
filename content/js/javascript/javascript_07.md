---
author: "li_mingxie"
title: "【JavaScript笔记】JavaScript基础_this和原型(7)"
date: 2022-05-07T09:05:49+08:00
tags: [
    "js",
    "javascript",
]
categories: [
    "JavaScript",
]
---

这里整理了函数中this和原型使用方法。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript">
        console.log("--------------------this--------------------");

        //this是函数里的隐含参数，指向函数执行时的上下文对象。
        //根据调用方式不一样，this会指向不同对象，
        //一般函数指向 window, 方法指向调用对象。
        function fun() {
            console.log(this);
        }

        var obj = {
            name: "张三",
            age: 18,
            sayThis: fun
        };

        fun();//Window {...}
        obj.sayThis(); //{name: '张三', age: 18, sayThis: ƒ fun()}
        console.log(obj.sayThis == fun); // true

    
        //简单的工厂方法创建类，一般不这么用，一般会用下面你的构造函数方法
        function CreatePerson(name, age) {
            var obj = new Object();
            obj.name = name;
            obj.age = age;
            obj.sayThis = fun;
            return obj;
        }

        var per1 = CreatePerson("张一", 18);
        var per2 = CreatePerson("张二", 20);
        console.log("per1:", per1); //{name: '张一', age: 18, sayThis: ƒ fun()}
        console.log("per2:", per2); //{name: '张二', age: 20, sayThis: ƒ fun()}


        //使用构造函数创建类
        /*
            1.使用new 创建一个对象
            2.将新建的对象设置为函数中的this
            3.执行函数中的代码
            4.将新建对象作为返回值返回
        */
        function Person(name, age) {
            this.name = name;
            this.age = age;
            this.sayHello = function () {
                console.log("Hello");
            }
            this.sayName = sayName;
        }


        function sayName() {
            console.log("我叫:" + this.name);
        }

        var person = new Person();
        //使用instanceof判断是否是Person类的实例
        console.log(person instanceof Person); //true

        var person1 = new Person();
        var person2 = new Person();
        console.log(person1.sayHello == person2.sayHello); //false
        console.log(person1.sayName == person2.sayName); //true

        //直接声明function会污染全局变量，不知道下一段代码里有人又声明了相同的function覆盖掉
        //这时候使用prototype模式, 他是每个Object都有的隐含的属性，他会自动指向自己的prototype对象(原型对象)
        console.log(person.__proto__ == Person.prototype); //true

        //原型中添加属性
        Person.prototype.a = 123;
        console.log(person.a); //123
        console.log(person1.a); //123

        //当原型和实例都存在的时候
        person.a = "abc";
        console.log(person.a); //abc  先在自己实例中的a属性，如果没有再去找prototype中的a属性
        console.log(person1.a); //123

        //查看属性存不存在、
        Person.prototype.gender = "男";
        console.log("gender" in person); //true 也是先找自己，再找prototype
        console.log(person.hasOwnProperty("gender")); //false 只查看自己，不看prototype

        //存在这多层的prototype原型
        console.log(person.__proto__.hasOwnProperty("hasOwnProperty")); //false
        console.log(person.__proto__.__proto__.hasOwnProperty("hasOwnProperty")); //true
        console.log(person.__proto__.__proto__.__proto__); //null 一般2层的prototype

    </script>
</head>

<body>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
