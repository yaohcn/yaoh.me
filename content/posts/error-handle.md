---
title: "error handle"
date: 2020-01-05T19:47:52+08:00
draft: false
---
按照惯例，错误通常是最后一个返回值并且是 error 类
型，一个内建的接口。常见用法如下。
```
$ godoc builtin error
use 'godoc cmd/builtin' for documentation on the builtin command

type error interface {
    Error() string
}
    The error built-in interface type is the conventional interface for
    representing an error condition, with the nil value representing no
    error.

```
#### errors.New("error message")构建一个基本的错误类型
```go
// Package errors implements functions to manipulate errors.
package errors

// New returns an error that formats as the given text.
func New(text string) error {
	return &errorString{text}
}

// errorString is a trivial implementation of error.
type errorString struct {
	s string
}

func (e *errorString) Error() string {
	return e.s
}
```
#### 返回的错误信息如果需要包含上下文信息。使用fmt.Errorf
```go
if f < 0 {
    return 0, fmt.Errorf("math: square root of negative number %g", f)
}
```
#### error是一个接口，也可以自定义，如json包中的SyntaxError
```go
type SyntaxError struct {
    msg    string // description of error
    Offset int64  // error occurred after reading Offset bytes
}

func (e *SyntaxError) Error() string { return e.msg }
```
使用[类型断言](https://golang.org/ref/spec#Type_assertions)判断错误的具体类型并作处理
```go
if err := dec.Decode(&val); err != nil {
    if serr, ok := err.(*json.SyntaxError); ok {
        line, col := findLine(f, serr.Offset)
        return fmt.Errorf("%s:%d:%d: %v", f.Name(), line, col, err)
    }
    return err
}
```
#### 使用github.com/pkg/errors包装errors
使用[errors](https://github.com/pkg/errors)包，你可以以人和机器都可检查的方式向错误值添加上下文。

#### 错误和异常
* 广义上的错误：发生非期望的行为
* 狭义上的错误：发生非期望的已知行为
* 异常：发生非期望的未知行为，又被称为未捕获的错误(untrapped error)

go对于错误提供了两种处理机制
* 通过函数返回错误类型的值来处理错误
* 通过panic打印程序调用栈，终止程序来处理错误

go是一门类型安全的语言，其运行时不会出现这种编译器和运行时都无法捕获的错误，也即不会出现untrap
ped error

何时使用error，何时使用panic两条规则：
* 程序发生的错误导致程序不能容错继续执行，此时应调用panic或由运行时抛出panic
* 虽然发生错误，但是程序能容错继续执行，此时应使用错误返回的方式，或者在可能发生运行时错误的非关键分支上使用recover捕获panic
#### 其他注意事项
1.错误处理的另一种写法
```go
if err := datastore.Get(c, key, record); err != nil {
    http.Error(w, err.Error(), 500)
    return
}
```
2.错误不能忽略，但也不需要处理多次，只需要处理一次。
如果发生错误时函数内部和函数调用者都处理了该错误，并记录到日志文件中，则日志文件中会有很多重复内容。  

3.处理错误时go的风格是使用guard clauses。

4.defer语句应该放在err判断的后面，不然可能产生panic

### reference
- [Error handling and Go](https://blog.golang.org/error-handling-and-go)
- [Practical Go: Real world advice for writing maintainable Go programs](https://dave.cheney.net/practical-go/presentations/qcon-china.html#_error_handling)
- [Replace Nested Conditional with Guard Clauses](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)
- [If-statements design: guard clauses may be all you need](https://medium.com/@scadge/if-statements-design-guard-clauses-might-be-all-you-need-67219a1a981a)
- [dont-just-check-errors-handle-them-gracefully](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully)
- Go语言核心编程（2.6错误处理）

