---
title: Golang语法系列——recover异常处理 
excerpt: Golang 没有结构化异常，使用 panic 抛出错误，recover 捕获错误。
         异常的使用场景简单描述：Go中可以抛出一个panic的异常，然后在defer中通过recover捕获这个异常，然后正常处理。
index_img: https://static.bkduck.cn/img/blog/go-202109281108.png
date: 2021-08-04
updated: 2021-08-04
categories:
  - Golang
tags:
  - golang
  - go语法 
---

## 现象 

- `panic` 只会触发当前 Goroutine 的 `defer`；
- `recover` 只有在 `defer` 中调用才会生效；
- `panic` 允许在 `defer` 中嵌套多次调用；

### 跨协程失效

`panic` 只会触发当前 Goroutine 的延迟函数调用

```go
func main() {
	defer println("in main")
	go func() {
		defer println("in goroutine")
		panic("")
	}()

	time.Sleep(1 * time.Second)
}

$ go run main.go
in goroutine
panic:
...
```

当我们运行这段代码时会发现 `main` 函数中的 `defer` 语句并没有执行，执行的只有当前 Goroutine 中的 `defer`

### 失效的崩溃恢复

在主程序中调用 `recover` 试图中止程序的崩溃，但是从运行的结果中我们也能看出，下面的程序没有正常退出。

```go
func main() {
	defer fmt.Println("in main")
	if err := recover(); err != nil {
		fmt.Println(err)
	}

	panic("unknown err")
}

$ go run main.go
in main
panic: unknown err

goroutine 1 [running]:
main.main()
	...
exit status 2
```

`recover` 只有在发生 `panic` 之后调用才会生效。然而在上面的控制流中，`recover` 是在 `panic` 之前调用的，并不满足生效的条件，所以**我们需要在 `defer` 中使用 `recover` 关键字**。

### 嵌套崩溃

```go
func main() {
	defer func(){
		fmt.Println("5")
		fmt.Println("in main")
	}()
	defer func() {
		fmt.Println("2")
		defer func() {
			fmt.Println("4")
			panic("panic again and again and again")
		}()
		defer func() {
			fmt.Println("3")
			panic("panic again and again")
		}()
		panic("panic again")
	}()
	fmt.Println("1")
	panic("panic once") 
}

>>>
1
2
4
5
6
in main

panic: panic once
	panic: panic again
	panic: panic again and again
	panic: panic again and again and again
exit status 2
```

`panic` 先执行同级`defer`,然后同级`defer`按FILO顺序执行，若`defer`内部嵌套`defer`, 其执行顺序按内部嵌套FILO顺序执行。同时**注意`panic`位置在`defer` 前面则同级后面`defer`不会执行**



### panic()和recover()和defer运用

- 程序panic，defer中使用recover恢复
```go
#执行panic，这时recover就不等于nil，然后程序先执行defer，然后panic终止
#类似于try catch
package main

import (
	"log"
)

func protect(g func()) {
	defer func() {
		log.Println("done")
		// Println executes normally even if there is a panic
		if err := recover(); err != nil {
			log.Printf("run time panic: %v", err)
		}
	}()
	log.Println("start")
	g() //   possible runtime-error
}
func main() {
	protect(func() {
		log.Println("in g()")
		panic("dropout g()")
	})
}

输出
2018/07/25 18:21:23 start
2018/07/25 18:21:23 in g()
2018/07/25 18:21:23 done
2018/07/25 18:21:23 run time panic: dropout g()

```
