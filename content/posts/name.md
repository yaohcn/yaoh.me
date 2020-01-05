---
title: "golang name"
date: 2020-01-05T19:47:52+08:00
draft: false
---
# 命名
## 包名
包名应该为小写单词，不要使用下划线或者混合大小写
## 接口名
1. 单个函数的接口名以"er"作为后缀，如Reader,Writer。接口的实现则去掉"er"
```go
type Reader interface {
	Read(p []byte) (n int, err error)	
}
```
2. 两个函数的接口名综合两个函数名
```go
type WriteFlusher interface {
	Write([]byte) (int, error)
	Flush() error
}
```
3. 三个以上函数的接口名，类似于结构体名
```go
type Car interface {
	Start([]byte) 
	Stop() error
	Recover()
}
```
