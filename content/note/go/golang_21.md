---
author: "li_mingxie"
title: "【golang笔记】go_json的序列化和反序列化(21)"
date: 2022-09-22T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`json的序列化和反序列化`** 相关的内容。<!--more-->

## 1.json概念

JavaScript Object Notation =》 key-value的轻量级的数据交换格式。  

## 2.json的序列化

> data, err := json.Marshal(&object)

```go
package main

import (
 "encoding/json"
 "fmt"
)

//定义一个结构体
type Monster struct {
 Name     string `json:"monster_name"` //反射机制
 Age      int    `json:"monster_age"`
 Birthday string //....
}

func testStruct() {
 //演示
 monster := Monster{
  Name:     "牛魔王",
  Age:      500,
  Birthday: "2011-11-11",
 }

 //将monster 序列化
 data, err := json.Marshal(&monster) //..
 if err != nil {
  fmt.Printf("序列号错误 err=%v\n", err)
 }
 //输出序列化后的结果
 fmt.Printf("monster序列化后=%v\n", string(data))
}

//将map进行序列化
func testMap() {
 //定义一个map
 var a map[string]interface{}
 //使用map,需要make
 a = make(map[string]interface{})
 a["name"] = "红孩儿"
 a["age"] = 30
 a["address"] = "洪崖洞"

 //将a这个map进行序列化
 //将monster 序列化
 data, err := json.Marshal(a)
 if err != nil {
  fmt.Printf("序列化错误 err=%v\n", err)
 }
 //输出序列化后的结果
 fmt.Printf("a map 序列化后=%v\n", string(data))
}

//演示对切片进行序列化, 我们这个切片 []map[string]interface{}
func testSlice() {
 var slice []map[string]interface{}
 var m1 map[string]interface{}
 //使用map前，需要先make
 m1 = make(map[string]interface{})
 m1["name"] = "jack"
 m1["age"] = "7"
 m1["address"] = "北京"
 slice = append(slice, m1)

 var m2 map[string]interface{}
 //使用map前，需要先make
 m2 = make(map[string]interface{})
 m2["name"] = "tom"
 m2["age"] = "20"
 m2["address"] = [2]string{"墨西哥", "夏威夷"}
 slice = append(slice, m2)

 //将切片进行序列化操作
 data, err := json.Marshal(slice)
 if err != nil {
  fmt.Printf("序列化错误 err=%v\n", err)
 }
 //输出序列化后的结果
 fmt.Printf("slice 序列化后=%v\n", string(data))

}

//对基本数据类型序列化，对基本数据类型进行序列化意义不大
func testFloat64() {
 var num1 float64 = 2345.67

 //对num1进行序列化
 data, err := json.Marshal(num1)
 if err != nil {
  fmt.Printf("序列化错误 err=%v\n", err)
 }
 //输出序列化后的结果
 fmt.Printf("num1 序列化后=%v\n", string(data))
}

func main() {
 //演示将结构体, map , 切片进行序列号
 testStruct()
 testMap()
 testSlice()   //演示对切片的序列化
 testFloat64() //演示对基本数据类型的序列化
}
```

## 3.json的反序列化

> var str = json
> var a interface{}
> err := json.Unmarshal([]byte(str), &a)

```go
package main

import (
 "encoding/json"
 "fmt"
)

//定义一个结构体
type Monster struct {
 Name     string
 Age      int
 Birthday string //....
}

//演示将json字符串，反序列化成struct
func unmarshalStruct() {
 //说明str 在项目开发中，是通过网络传输获取到.. 或者是读取文件获取到
 str := "{\"Name\":\"牛魔王~~~\",\"Age\":500,\"Birthday\":\"2011-11-11\"}"

 //定义一个Monster实例
 var monster Monster

 err := json.Unmarshal([]byte(str), &monster)
 if err != nil {
  fmt.Printf("unmarshal err=%v\n", err)
 }
 fmt.Printf("反序列化后 monster=%v monster.Name=%v \n", monster, monster.Name)
}

//将map进行序列化
func testMap() string {
 //定义一个map
 var a map[string]interface{}
 //使用map,需要make
 a = make(map[string]interface{})
 a["name"] = "红孩儿~~~~~~"
 a["age"] = 30
 a["address"] = "洪崖洞"

 //将a这个map进行序列化
 //将monster 序列化
 data, err := json.Marshal(a)
 if err != nil {
  fmt.Printf("序列化错误 err=%v\n", err)
 }
 //输出序列化后的结果
 //fmt.Printf("a map 序列化后=%v\n", string(data))
 return string(data)
}

//演示将json字符串，反序列化成map
func unmarshalMap() {
 //str := "{\"address\":\"洪崖洞\",\"age\":30,\"name\":\"红孩儿\"}"
 str := testMap()
 //定义一个map
 var a map[string]interface{}

 //反序列化
 //注意：反序列化map,不需要make,因为make操作被封装到 Unmarshal函数
 err := json.Unmarshal([]byte(str), &a)
 if err != nil {
  fmt.Printf("unmarshal err=%v\n", err)
 }
 fmt.Printf("反序列化后 a=%v\n", a)

}

//演示将json字符串，反序列化成切片
func unmarshalSlice() {
 str := "[{\"address\":\"北京\",\"age\":\"7\",\"name\":\"jack\"}," +
  "{\"address\":[\"墨西哥\",\"夏威夷\"],\"age\":\"20\",\"name\":\"tom\"}]"

 //定义一个slice
 var slice []map[string]interface{}
 //反序列化，不需要make,因为make操作被封装到 Unmarshal函数
 err := json.Unmarshal([]byte(str), &slice)
 if err != nil {
  fmt.Printf("unmarshal err=%v\n", err)
 }
 fmt.Printf("反序列化后 slice=%v\n", slice)
}

func main() {
 unmarshalStruct()
 unmarshalMap()
 unmarshalSlice()
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
