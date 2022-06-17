---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_文件读取和写入"
date: 2022-05-24T09:05:49+08:00
tags: [
    "Nodejs",
    "buffer",
    "fs",
    "write",
    "read",
    "stream",
]
categories: [
    "Nodejs",
]
---

这篇简单的整理了nodejs的buffer, 文件读取和写入, 以及流方式读取和写入。  <!--more-->

```js
{
    console.log('----------Buffer----------');
    /*
        Buffer(缓冲区)
        8bit=1字节byte
    */

    let str = 'hello 小明'

    let buf = Buffer.from(str);
    console.log(buf)//<Buffer 68 65 6c 6c 6f 20 62 75 66 66 65 72> 二进制表示为16进制
    //创建buffger
    console.log(buf.length); //12 内存长度
    console.log(str.length); //8  字符长度

    let buf2 = Buffer.alloc(10); //1024字节的Buffer
    console.log(buf2.length); //8  字符长度

    buf2[00] = 88;
    buf2[1] = 255;
    buf2[2] = 0xaa;
    buf2[3] = 256; // 只取后面的截取值。
    buf2[10] = 10; //没效果，因为buffer 长度确定后就不能修改。
    console.log(buf2); //<Buffer 58 ff aa 00 00 00 00 00 00 00>
    console.log(buf2[1].toString(16)); // ff


    //buffer 中有 敏感数据 使用 Buffer.allocUnsafe(); //他分配你空间的时候，没清空数据。性能会好。
    let buf3 = Buffer.allocUnsafe(10);
    console.log(buf3); //<Buffer 00 00 00 00 00 00 00 00 00 21>

    let buf4 = Buffer.from("你好，");
    //缓冲区二进制转换字符
    console.log(buf4.toString());//你好，
}

console.log('----------fs File System----------');
{
    const fs = require('fs');

    /*  1.打开文件
    fs 模块一般都有同步和异步两种形式。
    path:
    flags: r 只读 w 写入
    */
    let testFile = fs.openSync('./hello.txt', 'w');
    /* 2.写入文件
    fd: 要写入的对象。
    string: 写入内容
    postion: 写入的位置
    encoding : 默认 utf8
    */
    fs.writeSync(testFile, "你好，说中文。", 10);

    // 3.保存并关闭
    fs.close(testFile);
}
{
    //异步文件写入
    const fs = require('fs');

    fs.open('./hello.txt', 'a', function (err, fd) {
        if (err) {
            console.log(err)
        } else {
            fs.write(fd, "你好，写中文。", function (err) {
                if (err) {
                    console.log(err);
                } else {
                    fs.close(fd, function (err) {
                        if (err) {
                            console.log(err);
                        }
                    });
                }
            });
        }
    });
}

{
    const fs = require('fs');
    fs.writeFile('./aa.txt', '这是测试', function (err) {
        if (!err) {
            console.log("写入成功");
        }
    });
}

{
    //流逝写入
    const fs = require('fs');
    var ws = fs.createWriteStream('./bb.txt');

    //可以给对象绑定一个事件
    // ws.on('open', function () {
    //     console.log('流打开了');
    // });

    //可以给我们对象绑定一次性事件
    ws.once('open', function () {
        console.log('流打开了');
    });

    ws.once('close', function () {
        console.log('流关闭了');
    });

    ws.write('通过可写流写入1');
    ws.write('通过可写流写入2');
    ws.write('通过可写流写入3');
    ws.write('通过可写流写入4');
    ws.write('通过可写流写入5');
    ws.write('通过可写流写入6');

    //关闭流  不能用 ws.close()  因为还没传完就会关闭
    ws.end();
}

/*
{
    const fs = require('fs');
    var rs = fs.createReadStream('/Users/limingxie/Desktop/Like A Cat.mp4');
    // var ws = fs.createWriteStream('');

    rs.once('open', function () {
        console.log('可读流打开了');
    });

    rs.once('close', function () {
        console.log('可读流关闭了');
    });

    rs.on('data', function (data) {
        console.log(data.length);
        console.log(data);
    });
}
*/

//data 方法
{
    const fs = require('fs');
    var rs = fs.createReadStream('/Users/limingxie/Desktop/Like A Cat.mp4');
    var ws = fs.createWriteStream('/Users/limingxie/Desktop/aa.mp4');

    rs.once('open', function () {
        console.log('可读流打开了');
    });

    rs.once('close', function () {
        console.log('可读流关闭了');
        ws.end();// 读完后关闭
    });

    ws.once('open', function () {
        console.log('可写流打开了');
    });

    ws.once('close', function () {
        console.log('可写流关闭了');
    });

    rs.on('data', function (data) {
        // console.log(data.length);
        // console.log(data);
        ws.write(data);
    });
}

//pipe 方法
{
    const fs = require('fs');
    var rs = fs.createReadStream('/Users/limingxie/Desktop/Like A Cat.mp4');
    var ws = fs.createWriteStream('/Users/limingxie/Desktop/bb.mp4');

    rs.pipe(ws);
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
