---
title: "test"
date: 2020-01-05T19:47:52+08:00
draft: false
---
## 测试(Test)
#### 编写测试规则

- 它需要在一个名为xxx_test.go的文件中编写
- 测试函数的命名必须从单词Test开始
- 测试函数只接受一个参数 t *testing.T(类似测试框架中的hook钩子)

#### 计算测试覆盖率
[参考](https://blog.golang.org/cover)
```
[hua@fedora sum]$ go test -cover
PASS
coverage: 100.0% of statements
ok  	_/home/hua/github/code-note/go/TDD/sum	0.001s
```


## 示例(Example)
与典型的测试一样，示例存在与一个包的_test.go文件中  
可以与测试放在一个文件(adder_test.go)，也可以放在example_test.go文件中  
示例会出现在godoc文档中(godoc -ex .)。[参考](https://blog.golang.org/examples)
```go
//如果删除注释【//Output:6，示例函数不会执行，但会被编译
func ExampleAdd() {
	sum := Add(1, 5)
	fmt.Println(sum)
	//Output:6
}
```
## 基准测试(Benchmark)
基准测试，是一种测试代码性能的方法，基准测试主要是通过测试CPU和内存的效率问题，来评估被测试代码的性能。
- 基准测试的代码文件必须以_test.go结尾
- 基准测试的函数必须以Benchmark开头，必须是可导出的
- 基准测试函数必须接受一个指向Benchmark类型的指针作为唯一参数
- 基准测试函数不能有返回值
- b.ResetTimer是重置计时器，这样可以避免for循环之前的初始化代码的干扰最后的for循环很重要，被测试的代码要放到循环里
- b.N是基准测试框架提供的，表示循环的次数，因为需要反复调用测试的代码，才可以评估性能

## 其他
go tool支持在两个地方编写testing包测试。假设你的包名为http2，您可以编写http2_test.go文件并使用包http2声明。这样做会编译http2_test.go中的代码，就像它是http2包的一部分一样。这就是内部测试。

go tool还支持一个特殊的包声明，以test为结尾，即package http_test。这允许你的测试文件与代码一起存放在同一个包中，但是当编译时这些测试不是包的代码的一部分，它们存在于自己的包中。就像调用另一个包的代码一样来编写测试。这被称为外部测试。
