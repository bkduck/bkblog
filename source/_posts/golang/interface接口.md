---
title: Golang语法系列——interface接口
excerpt: 如果说goroutine和channel是Go并发的两大基石，那么接口是Go语言编程中数据类型的关键。在Go语言的实际编程中，几乎所有的数据结构都围绕接口展开，接口是Go语言中所有数据结构的核心
index_img: https://static.bkduck.cn/img/blog/go-202109281053.png
date: 2021-08-05
updated: 2021-08-05
categories:
  - Golang
tags:
  - golang
  - go语法  
---

### NOTE
- 主要概念
```
1. interface 可被 struct 等继承 （animalI = catT, catT 实现所有animalI的方法）
2. 了解规则：最好使用new()生成指针类
```

* [参考链接](https://sanyuesha.com/2017/07/22/how-to-understand-go-interface/)

### type-switch 类型选择
- 参考链接 
```
1. 介绍interface的用处
https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/02.6.md 
```

- Comma-ok断言 判断类型  element.(T)
```
	package main

	import (
		"fmt"
		"strconv"
	)

	type Element interface{}
	type List []Element

	type Person struct {
		name string
		age int
	}

	//打印
	func (p Person) String() string {
		return "(name: " + p.name + " - age: "+strconv.Itoa(p.age)+ " years)"
	}

	func main() {
		list := make(List, 3)
		list[0] = 1 //an int
		list[1] = "Hello" //a string
		list[2] = Person{"Dennis", 70}

		for index, element := range list{
			switch value := element.(type) {
				case int:
					fmt.Printf("list[%d] is an int and its value is %d\n", index, value)
				case string:
					fmt.Printf("list[%d] is a string and its value is %s\n", index, value)
				case Person:
					fmt.Printf("list[%d] is a Person and its value is %s\n", index, value)
				default:
					fmt.Println("list[%d] is of a different type", index)
			}
		}
	}
```

- interface 可以嵌套interface
```
例如io标准库
// io.ReadWriter
// Reader和Writer两个 interface
type ReadWriter interface {
	Reader
	Writer
}

```

### 类型断言
- 代码
```
#NOTE：如果忽略 areaIntf.(*Square) 中的 * 号，会导致编译错误：impossible type assertion: Square does not implement Shaper (Area method has pointer receiver)。

package main

import (
	"fmt"
	"math"
)

type Square struct {
	side float32
}

type Circle struct {
	radius float32
}

type Shaper interface {
	Area() float32
}

func main() {
	var areaIntf Shaper
	sq1 := new(Square)
	sq1.side = 5

	areaIntf = sq1
	// Is Square the type of areaIntf?
	if t, ok := areaIntf.(*Square); ok {
		fmt.Printf("The type of areaIntf is: %T\n", t)
	}
	if u, ok := areaIntf.(*Circle); ok {
		fmt.Printf("The type of areaIntf is: %T\n", u)
	} else {
		fmt.Println("areaIntf does not contain a variable of type Circle")
	}
}

func (sq *Square) Area() float32 {
	return sq.side * sq.side
}

func (ci *Circle) Area() float32 {
	return ci.radius * ci.radius * math.Pi
}

输出
The type of areaIntf is: *main.Square
areaIntf does not contain a variable of type Circle
```

### 调用规则
- 规则
```
Go 语言规范定义了接口方法集的调用规则：

    类型 *T 的可调用方法集包含接受者为 *T 或 T 的所有方法集
    类型 T 的可调用方法集包含接受者为 T 的所有方法
    类型 T 的可调用方法集不包含接受者为 *T 的方法

```

- 代码
```
#注意new()的用法

package main

import (
	"fmt"
)

type List []int

func (l List) Len() int {
	return len(l)
}

func (l *List) Append(val int) {
	*l = append(*l, val)
}

type Appender interface {
	Append(int)
}

func CountInto(a Appender, start, end int) {
	for i := start; i <= end; i++ {
		a.Append(i)
	}
}

type Lener interface {
	Len() int
}

func LongEnough(l Lener) bool {
	return l.Len()*10 > 42
}

func main() {
	// A bare value
	var lst List
	// compiler error:
	// cannot use lst (type List) as type Appender in argument to CountInto:
	//       List does not implement Appender (Append method has pointer receiver)
	// CountInto(lst, 1, 10)
	if LongEnough(lst) { // VALID:Identical receiver type
		fmt.Printf("- lst is long enough\n")
	}

	// A pointer value
	plst := new(List)
	CountInto(plst, 1, 10) //VALID:Identical receiver type
	if LongEnough(plst) {
		// VALID: a *List can be dereferenced for the receiver
		fmt.Printf("- plst is long enough\n")
	}
}

输出
- plst is long enough
```

### 空接口复制
- 代码
```
#需逐个显式复制
var dataSlice []myType = FuncReturnSlice()
var interfaceSlice []interface{} = make([]interface{}, len(dataSlice))
for i, d := range dataSlice {
    interfaceSlice[i] = d
}

#错误语法
var dataSlice []myType = FuncReturnSlice()
var interfaceSlice []interface{} = dataSlice
```






