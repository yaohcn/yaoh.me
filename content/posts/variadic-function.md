---
title: "variadic function"
date: 2020-01-05T19:47:52+08:00
draft: false
---
### Variadic Function 可变参数函数
```go
// [_Variadic functions_](http://en.wikipedia.org/wiki/Variadic_function)
// can be called with any number of trailing arguments.
// For example, `fmt.Println` is a common variadic
// function.

package main

import "fmt"

// Here's a function that will take an arbitrary number
// of `int`s as arguments.
func sum(nums ...int) {
	fmt.Printf("nums type: %T  ", nums) // nums type: []int
	fmt.Print(nums, " ")
	total := 0
	for _, num := range nums {
		total += num
	}
	fmt.Println(total)
}

func main() {

	// Variadic functions can be called in the usual way
	// with individual arguments.
	sum(1, 2)
	sum(1, 2, 3)

	// If you already have multiple args in a slice,
	// apply them to a variadic function using
	// `func(slice...)` like this.
	nums := []int{1, 2, 3, 4}
	sum(nums...)
}
```
在不定参数函数内部，不定参数的类型是一个slice。不定参数函数也可以接受slice作为一个参数，func(slice...)。   
参见fmt包中的用法：
```go
// Sprintf formats according to a format specifier and returns the resulting string.
func Sprintf(format string, a ...interface{}) string {
	p := newPrinter()
	p.doPrintf(format, a)
	s := string(p.buf)
	p.free()
	return s
}

// Errorf formats according to a format specifier and returns the string
// as a value that satisfies error.
func Errorf(format string, a ...interface{}) error {
    //在这个函数内部a是一个slice，所以下面的传参需要用a...
	return errors.New(Sprintf(format, a...))
}
```
### reference
- [Go by Example: Variadic Functions](https://gobyexample.com/variadic-functions)
