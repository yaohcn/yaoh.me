---
title: "type system"
date: 2020-01-05T19:47:52+08:00
draft: false
---
# 类型系统
## named type vs unamed type
### named type
- 预声明类型（[pre-declared type](https://golang.org/ref/spec#Predeclared_identifiers)）
- 自定义类型，用[type](https://golang.org/ref/spec#Type_declarations)关键字生成的类型，如 `type Foo string`
### unamed type
基于已有的named type声明出的组合类型，比如array,slice,map等([ ]Foo也是未命名类型)。  
未命名类型，类型字面量(type literal)，复合类型( Composite types)三者是等价的。

Named types 可以作为方法的接受者， unnamed type 却不能。  

**特别注意**：pre-declared types 也是命名类型，所以：int 是named type，但是不能直接为int定义method
```go
func (a int) methodname() {

}
```
不是因为int 是unnamed type ，而是为一个type定义方法必须在该类型的所在的package ,   
int 的scope （作用域是）universe (全局的)，int 是语言层面预声明的，其属于任何package,  
也就没有办法为其增加method.

### underlying type
每种类型T都有一个底层类型：如果T是预声明类型或者类型字面量，它的底层类型就是T本身，  
否则，T的底层类型是其类型声明中引用的类型的底层类型。
```go
type (
	B1 string
	B2 B1 
	B3 []B1
	B4 B3 
)
```
B1 和 B2 的底层类型是 string。  
B2 引用了 B1，那么 B2 的底层类型其实是 B1 的底层类型，而 B1 又引用了 string，  
那么 B1 的底层类型其实是 string 的底层类型，很明显，string 的底层类型就是string，  
最终 B2 的底层类型是 string。

[]B1, B3, 和 B4 的底层类型是 []B1  
[]B1 是类型字面量，因此它的底层类型就是它本身。

[查看底层类型](https://gist.github.com/yaohcn/024d1dd9aaf67dd16bad5d66877e6672)

只要是底层类型是slice,map等支持range的类型字面量，新类型仍然可以使用range迭代
```go
type Map map[string]string
func (m Map) Print(){
	for _,key:=range m{
		fmt.Println(key)
	}
}
```
### [Assignability](https://golang.org/ref/spec#Assignability)
不同类型的变量之间一般不能直接赋值。  
底层类型相同的两个变量可以赋值的条件是：至少有一个不是 named type。


## Reference
- [Go 迷思之 Named 和 Unnamed Types](http://hxzqlh.github.io/2017/11/27/go-Named-and-Unnamed-Types/)
- [spec#Types](https://golang.org/ref/spec#Types)
- Go语言核心编程（第3章类型系统）
