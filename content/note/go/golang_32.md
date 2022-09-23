---
author: "li_mingxie"
title: "【golang笔记】go_反射的实现(32)"
date: 2022-10-03T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`反射的实现`** 相关的内容。<!--more-->

## 1.反射实现中常用的几个方法

```

//TypeOf返回接口中保存的值的类型，TypeOf(nil)会返回nil。
func TypeOf(i interface{}) Type

//ValueOf返回一个初始化为i接口保管的具体值的Value，ValueOf(nil)返回Value零值。
func ValueOf(i interface{}) Value

//Kind返回v持有的值的分类，如果v是Value零值，返回值为Invalid
func (v Value) Kind() Kind

//返回v持有的结构体类型值的字段数，如果v的Kind不是Struct会panic
func (v Value) NumField() int

//返回结构体的第i个字段（的Value封装）。如果v的Kind不是Struct或i出界会panic
func (v Value) Field(i int) Value

// 返回索引序列指定的嵌套字段的类型，
// 等价于用索引中每个值链式调用本方法，如非结构体将会panic
Field(i int) StructField
type.Field(i)

//返回v持有值的方法集的方法数目。
func (v Value) NumMethod() int

//Call方法使用输入的参数in调用v持有的函数。例如，如果len(in) == 3，v.Call(in)代表调用v(in[0], in[1], in[2])（其中Value值表示其持有值）。  
如果v的Kind不是Func会panic。它返回函数所有输出结果的Value封装的切片。  
和go代码一样，每一个输入实参的持有值都必须可以直接赋值给函数对应输入参数的类型。如果v持有值是可变参数函数，  
Call方法会自行创建一个代表可变参数的切片，将对应可变参数的值都拷贝到里面。
func (v Value) Call(in []Value) []Value
value.Method(i).Call(nil)
```

## 2.反射实践

```go
package main

import (
 "fmt"
 "reflect"
)

//定义了一个Monster结构体
type Person struct {
 Name   string `json:"name"`
 Age    int    `json:"monster_age"`
 Weight int
}

func (p Person) Run() {
 fmt.Println("person run")
}

func (p Person) Hello(area string) {
 fmt.Printf("%v Hello %s\n", p.Name, area)
}

func (p Person) Sleep(area string, h int) string {
 return fmt.Sprintf("%v sleep in %s for %d hour\n", p.Name, area, h)
}

func TestStruct(p interface{}) {

 //获取reflect.Type 类型
 t := reflect.TypeOf(p)

 //获取reflect.Value 类型
 val := reflect.ValueOf(p)

 //获取到p对应的类别
 k := val.Kind()

 //如果传入的不是struct，就退出

 if k != reflect.Struct {
  fmt.Println("expect struct")
  return
 }

 //获取结构体的字段数
 fieldCount := val.NumField()

 for i := 0; i < fieldCount; i++ {
  fname := t.Field(i).Name
  fValue := val.FieldByName(fname)
  valTag := t.Field(i).Tag.Get("json")
  fmt.Printf("%s = %v, tag = %v\n", fname, fValue, valTag)
 }

 //获取到该结构体有多少个方法
 methodCount := val.NumMethod()
 fmt.Printf("struct has %d methods\n", methodCount)

 //var params []reflect.Value
 //方法的排序默认是按照 函数名的排序（ASCII码）
 val.Method(1).Call(nil) //获取到第二个方法。调用它

 //调用结构体的第3个方法Method(2)
 var params []reflect.Value
 params = append(params, reflect.ValueOf("bed"))
 params = append(params, reflect.ValueOf(2))

 //传入的参数是 []reflect.Value, 返回[]reflect.Value
 res := val.Method(2).Call(params)
 //返回结果, 返回的结果是 []reflect.Value*/
 fmt.Println("res=", res[0].String())
}

func main() {
 //创建了一个Monster实例
 p := Person{
  Name:   "tom",
  Age:    400,
  Weight: 55,
 }
 //将Monster实例传递给TestStruct函数
 TestStruct(p)
}
```

#### 执行结果

```bash
$ go run main.go
Name = tom, tag = name
Age = 400, tag = monster_age
Weight = 55, tag = 
struct has 3 methods
person run
res= tom sleep in bed for 2 hour
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
