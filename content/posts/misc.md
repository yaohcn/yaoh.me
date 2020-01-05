---
title: "misc"
date: 2020-01-05T19:47:52+08:00
draft: false
---
### const 
- 常量可以提高程序的性能，它避免了每次调用Hello时创建"Hello, "字符串实例
- 用常量表示错误信息，更方便写测试:var ErrNotFound = errors.New("could not find the word you were looking for")
### named result parameters
go函数的返回值可以被命名，就像函数的参数一样。命名返回值不是强制性的，  
当函数有多个返回值，或者函数很长时，为了清晰可以采用命名返回值
```go
// Location returns f's latitude and longitude.
// Negative values mean south and west, respectively.
func (f *Foo) Location() (lat, long float64, err error)
```
详细参考[named-result-parameters](https://github.com/golang/go/wiki/CodeReviewComments#named-result-parameters)和[named-results](https://golang.org/doc/effective_go.html#named-results)
### reflect.DeepEqual
### 指针
- 当你传值给函数或方法时，Go 会复制这些值。因此，如果你写的函数需要更改状态，你就需要用指针指向你想要更改的值
- Go 取值的副本在大多数时候是有效的，但是有时候你不希望你的系统只使用副本，在这种情况下你需要传递一个引用。例如，非常庞大的数据或者你只想有一个实例（比如数据库连接池）
- map是一个引用类型，不用传递指针就可以更改状态
### map
map的值可以是nil，当使用一个值为nil的map时，会得到nil指针异常  

```
//不应该初始化一个空的map
var m map[string]string
//正确的方法
dictionary = map[string]string{}
// OR
dictionary = make(map[string]string)
```
### 依赖注入
- 将依赖关系定义成一个接口，实现解耦

### package main
- 确保main包内容尽可能的少，main应该做解析flags，开启数据库连接、开启日志等，然后将执行交给更高一级的对象。
- 如果Go语言程序的main.main函数返回，无论程序在一段时间内启动的其他goroutine在做什么, Go语言程序会无条件地退出。  
- 只在main.main或init函数中的使用log.Fatal。  

### 使用internal包来减少公共API
[internalpackages](https://golang.org/doc/go1.4#internalpackages)
### golang 配置文件路径问题。
- 配置文件和可执行文件位置相同，程序中获取可执行文件的位置exec.LookPath(os.Args[0]) 。
- 配置文件作为命令行参数可配置。
### select 
- 除 default 外，如果只有一个 case 语句评估通过，那么就执行这个case里的语句；
- 除 default 外，如果有多个 case 语句评估通过，那么通过伪随机的方式随机选一个；
- 如果 default 外的 case 语句都没有通过评估，那么执行 default 里的语句；
- 如果没有 default，那么 代码块会被阻塞，指导有一个 case 通过评估；否则一直阻塞
### database/sql
每次 db.Query 操作后, 都需要调用 rows.Close(). 因为 db.Query() 会从数据库连接池中获取一个连接, 如果我们没有调用 rows.Close(), 则此连接会一直被占用. 因此通常我们使用 defer rows.Close() 来确保数据库连接可以正确放回到连接池中.
