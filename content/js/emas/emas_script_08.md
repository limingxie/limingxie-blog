---
author: "li_mingxie"
title: "【es6笔记】es6的入门简介_class(8)"
date: 2022-05-17T09:05:49+08:00
tags: [
    "es6",
    "class",
    "static",
]
categories: [
    "Emas",
]
---

这里整理了Class相关的内容。

```html
<!DOCType html>
<html>

<head>
    <meta charset="utf-8">
    <title></title>
</head>

<body>
    <script>
        console.log("-------------- Class--------------");
        {
            //ES5的方式
            function Phone(brand, price) {
                this.brand = brand;
                this.price = price;
            }

            Phone.prototype.call = function () {
                console.log('打电话');
            }

            let huawei = new Phone('华为', '2000');
            console.log(huawei); //Phone {brand: '华为', price: '2000'}
            huawei.call(); //打电话
        }

        {
            //ES6的方式
            class Phone {
                constructor(brand, price) {
                    this.brand = brand;
                    this.price = price;
                }

                call() {
                    console.log('打电话');
                }
            }

            let huawei = new Phone('华为', '2000');
            console.log(huawei);//Phone {brand: '华为', price: '2000'}
            huawei.call(); //打电话
        }

        console.log("-------------- Class 静态成员 --------------");
        {
            function Phone() {

            }

            //静态成员
            Phone.name = '华为';
            Phone.call = function () {
                console.log('打电话');
            }

            //实例对象
            Phone.prototype.size = '5.5'

            let huawei = new Phone();
            console.log(huawei.name); //undefined
            // huawei.call(); //TypeError: huawei.call is not a function
            console.log(huawei.size); //5.5
        }

        {
            class Phone {
                //静态属性
                static name = '手机';
                static call() {
                    console.log('打电话');
                }
            }

            let huawei = new Phone();
            console.log(huawei.name); //undefined
            console.log(Phone.name); //手机

        }

        console.log("-------------- Class 构造函数继承 --------------");

        //ES5构造函数继承
        {
            //手机
            function Phone(brand, price) {
                this.brand = brand;
                this.price = price;
            }

            Phone.prototype.call = function () {
                console.log('打电话');
            }

            //智能手机
            function SmartPhone(brand, price, color, size) {
                Phone.call(this, brand, price);
                this.color = color;
                this.size = size;
            }

            //设置机子构造函数的原理
            SmartPhone.prototype = new Phone;
            SmartPhone.prototype.constructor = SmartPhone;

            //设置智能手机的方法
            SmartPhone.prototype.playGame = function () {
                console.log('玩游戏');
            }

            SmartPhone.prototype.photo = function () {
                console.log('拍照');
            }

            const huawei = new SmartPhone('华为', '2000', '白色', '5.5');
            console.log(huawei); //SmartPhone {brand: '华为', price: '2000', color: '白色', size: '5.5'}
        }

        //ES6构造函数继承
        {
            class Phone {
                constructor(brand, price) {
                    this.brand = brand;
                    this.price = price;
                }

                call() {
                    console.log('打电话');
                }
            }

            class SmartPhone extends Phone {
                constructor(brand, price, color, size) {
                    super(brand, price);
                    this.color = color;
                    this.size = size;
                }

                playGame() {
                    console.log('玩游戏');
                }

                photo() {
                    console.log('拍照');
                }

                call() {
                    console.log('视频通话');
                }
            }

            const huawei = new SmartPhone('华为', '2000', '白色', '5.5');
            console.log(huawei); //SmartPhone {brand: '华为', price: '2000', color: '白色', size: '5.5'}
            huawei.call();//视频通话  不能调用父类方法。
        }

        console.log("-------------- Class get set --------------");

        {
            class Phone {
                constructor(brand, price) {
                    this.brand = brand;
                    this.price = price;
                }

                get price() {
                    return this._price - 50;
                }

                //set方法需要参数
                set price(val) {
                    this._price = val + 100;
                }
            }

            const huawei = new Phone('华为', '2000');
            console.log(huawei);//Phone {brand: '华为', price: '2000'}
            huawei.price = 300;
            console.log(huawei.price);//300 + 100 - 50 = 350
        }

    </script>
</body>

</html>
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
