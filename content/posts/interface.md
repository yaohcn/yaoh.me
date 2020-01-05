---
title: "interface"
date: 2020-01-05T19:47:52+08:00
draft: false
---
## interface
接口是一个抽象的类型，接口像一层胶水，可以灵活的解耦软件的每一个层次
### 空接口 interface{ }
由于空接口的方法集为空，所以任意类型都被认为实现了空接口，任意类型的实例都可以赋值或传递给空接口
### 编译器自动检测类型是否实现接口
```go
package main

import "io"

type myWriter struct {

}

/*func (w myWriter) Write(p []byte) (n int, err error) {
    return
}*/

func main() {
    // 检查 *myWriter 类型是否实现了 io.Writer 接口
    var _ io.Writer = (*myWriter)(nil)

    // 检查 myWriter 类型是否实现了 io.Writer 接口
    var _ io.Writer = myWriter{}
}
```
赋值语句会发生隐式地类型转换，在转换的过程中，编译器会检测等号右边的类型是否实现了等号左边接口所规定的函数。
### 如果实现了接收者是值类型的方法，会隐含地也实现了接收者是指针类型的方法
```go
package main

import "fmt"

type coder interface {
    code()
    debug()
}

type Gopher struct {
    language string
}

func (p Gopher) code() {
    fmt.Printf("I am coding %s language\n", p.language)
}

func (p *Gopher) debug() {
    fmt.Printf("I am debuging %s language\n", p.language)
}

func main() {
    var c coder = &Gopher{"Go"}
	//var c coder = Gopher{"Go"}
	//Gopher does not implement coder (debug method has pointer receiver)
    c.code()
    c.debug()
}
```
### 类型断言（type assertion）
接口类型断言的语法形式:
> i.(TypeName)

i必须是接口变量，TypeName可以是具体类型也可以是接口类型。  
#### 接口断言的两种语法表现
#####  直接赋值模式 `o := i.(TypeName)`
1. TypeName是具体类型，此时如果接口i绑定的实例类型就是具体类型TypeName，则变量o的类型就是TypeName  
变量o的值就是接口绑定的实例值得副本
2. TypeName是接口类型，如果接口i绑定的实例类型满足接口类型TypeName，则变量o的类型就是接口类型TypeName  
o底层绑定的具体类型实例是i绑定的实例的副本
3. 如果上述两种情况都不满足，则程序抛出panic
##### comma,ok表达式模式
```go
if o, ok := i.(TypeName); ok {

}
```
### 类型查询（type switches）
接口类型查询的语法格式如下：
```go
switch v := i.(type) {
	case type1:
		xxxx
	case type2:
		xxxx
	default:
		xxxx
}
```
### 接口的动态类型和静态类型
#### 动态类型
接口绑定的具体实列的类型称为接口的动态类型
#### 静态类型
接口被定义时，其类型就已经确定，这个类型叫接口的静态类型。接口的静态类型在其定义时就被确定，静态类型的本质特征就是方法签名集合。  
Go编译器校验接口能否赋值，是比较二者的方法集。
