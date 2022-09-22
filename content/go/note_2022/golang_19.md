---
author: "li_mingxie"
title: "【golang笔记】go_文件操作(19)"
date: 2022-09-20T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`文件操作`** 相关的内容。<!--more-->

## 1.os.File

详细内容可以到看官网的说明文档  
英文： <https://pkg.go.dev/os@go1.19.1#File>  
中文： <https://studygolang.com/pkgdoc>

```go
type File
func Create(name string) (file *File, err error)
func Open(name string) (file *File, err error)
func OpenFile(name string, flag int, perm FileMode) (file *File, err error)
func NewFile(fd uintptr, name string) *File
func Pipe() (r *File, w *File, err error)
func (f *File) Name() string
func (f *File) Stat() (fi FileInfo, err error)
func (f *File) Fd() uintptr
func (f *File) Chdir() error
func (f *File) Chmod(mode FileMode) error
func (f *File) Chown(uid, gid int) error
func (f *File) Readdir(n int) (fi []FileInfo, err error)
func (f *File) Readdirnames(n int) (names []string, err error)
func (f *File) Truncate(size int64) error
func (f *File) Read(b []byte) (n int, err error)
func (f *File) ReadAt(b []byte, off int64) (n int, err error)
func (f *File) Write(b []byte) (n int, err error)
func (f *File) WriteString(s string) (ret int, err error)
func (f *File) WriteAt(b []byte, off int64) (n int, err error)
func (f *File) Seek(offset int64, whence int) (ret int64, err error)
func (f *File) Sync() (err error)
func (f *File) Close() error
```

## 2.bufio.NewReader

```go
package main

import (
 "bufio"
 "fmt"
 "io"
 "os"
)

func main() {
 //打开文件
 file, err := os.Open("./test.txt")
 if err != nil {
  fmt.Println("open file err=", err)
 }

 //当函数退出时，要及时的关闭file
 defer file.Close() //要及时关闭file句柄，否则会有内存泄漏.

 // 创建一个 *Reader  ，是带缓冲的
 /*
  const (
  defaultBufSize = 4096 //默认的缓冲区为4096
  )
 */
 reader := bufio.NewReader(file)
 //循环的读取文件的内容
 for {
  str, err := reader.ReadString('\n') // 读到一个换行就结束
  if err == io.EOF {                  // io.EOF表示文件的末尾
   break
  }
  //输出内容
  fmt.Printf(str)
 }

 fmt.Println("文件读取结束...")
}
```

## 3.ioutil.ReadFile

```go
package main
import (
 "fmt"
 "io/ioutil" 
)
func main() {

 //使用ioutil.ReadFile一次性将文件读取到位
 file := "./test.txt"
 content, err := ioutil.ReadFile(file)
 if err != nil {
  fmt.Printf("read file err=%v", err)
 }
 //把读取到的内容显示到终端
 //fmt.Printf("%v", content) // []byte
 fmt.Printf("%v", string(content)) // []byte
 
 //我们没有显式的Open文件，因此也不需要显式的Close文件
 //因为，文件的Open和Close被封装到 ReadFile 函数内部
}
```

## 4.os.OpenFile

```go
package main
import (
 "fmt"
 "bufio"
 "os" 
)
func main() {
 //创建一个新文件，写入内容 5句 "hello, Gardon"
 //1 .打开文件 ./abc.txt
 filePath := "./abc.txt"
 file, err := os.OpenFile(filePath, os.O_WRONLY | os.O_CREATE, 0666)
 if err != nil {
  fmt.Printf("open file err=%v\n", err)
  return 
 }
 //及时关闭file句柄
 defer file.Close()
 //准备写入5句 "hello, Gardon"
 str := "hello,Gardon\r\n" // \r\n 表示换行
 //写入时，使用带缓存的 *Writer
 writer := bufio.NewWriter(file)
 for i := 0; i < 5; i++ {
  writer.WriteString(str)
 }
 //因为writer是带缓存，因此在调用WriterString方法时，其实
 //内容是先写入到缓存的,所以需要调用Flush方法，将缓冲的数据
 //真正写入到文件中， 否则文件中会没有数据!!!
 writer.Flush()
}
```

## 5.复制

```go
package main
import (
 "fmt"
 "io/ioutil" 
)
func main() {
 //将d:/abc.txt 文件内容导入到  e:/kkk.txt
 //1. 首先将  d:/abc.txt 内容读取到内存
 //2. 将读取到的内容写入 e:/kkk.txt
 file1Path := "d:/abc.txt" 
 file2Path := "e:/kkk.txt" 
 data, err := ioutil.ReadFile(file1Path)
 if err != nil {
  //说明读取文件有错误
  fmt.Printf("read file err=%v\n", err)
  return
 }
 err = ioutil.WriteFile(file2Path, data, 0666)
 if err != nil {
  fmt.Printf("write file error=%v\n", err)
 }
}
```

```go
package main
import (
 "fmt"
 "os"
 "io"
 "bufio" 
)

//自己编写一个函数，接收两个文件路径 srcFileName dstFileName
func CopyFile(dstFileName string, srcFileName string) (written int64, err error) {

 srcFile, err := os.Open(srcFileName)
 if err != nil {
  fmt.Printf("open file err=%v\n", err)
 }
 defer srcFile.Close()
 //通过srcfile ,获取到 Reader
 reader := bufio.NewReader(srcFile)

 //打开dstFileName
 dstFile, err := os.OpenFile(dstFileName, os.O_WRONLY | os.O_CREATE, 0666)
 if err != nil {
  fmt.Printf("open file err=%v\n", err)
  return 
 }

 //通过dstFile, 获取到 Writer
 writer := bufio.NewWriter(dstFile)
 defer dstFile.Close()

 return io.Copy(writer, reader)
}

func main() {
 //将d:/flower.jpg 文件拷贝到 e:/abc.jpg

 //调用CopyFile 完成文件拷贝
 srcFile := "./jpg/flower.jpg"
 dstFile := "./back/abc.jpg"
 _, err := CopyFile(dstFile, srcFile)
 if err == nil {
  fmt.Printf("拷贝完成\n")
 } else {
  fmt.Printf("拷贝错误 err=%v\n", err)
 }
}
```

## 其它

判断文件是否存在

```go
func PathExists(path string) (bool, error) {
 _, err := os.Stat(path)
 if err == nil { //文件目录存在
  return true, nil
 }
 if os.IsNotExist(err) {
  return false, nil
 }
 return false, err
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
