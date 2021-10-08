---
title: Golang语法系列——string字符串
excerpt: Go 语言字符串是一种值 类型，且值不可变，即创建某个文本后你无法再次修改这个文本的内容，也可以理解，字符串是字节的定长 数组。
index_img: https://static.bkduck.cn/img/blog/go-202109281113.png
date: 2021-08-10
updated: 2021-08-10
categories:
  - Golang
tags:
  - golang
  - go语法   
---

## 常用方法

### 修改字符串

```go
1. byte类型
s := "hello"
s2 := "你好"
c := []byte(s)  // 将字符串 s 转换为 []byte 类型
c2 := []rune(s2) // 转换中文unicode 占4个字节
c[0] = 'c'
s2 := string(c)  // 再转换回 string 类型
fmt.Printf("%s\n", s2)

2.切片
s := "hello"
s = "c" + s[1:] // 字符串虽不能更改，但可进行切片操作
fmt.Printf("%s\n", s)

```

### 连接字符串

```go
// + 号链接 
s := "hello,"
m := " world"
a := s + m
fmt.Printf("%s\n", a)

```

### 多行字符串

```go
m := `hello
  world`
```

### 判断字符长度

```go
// 判断中文
str := "hello 你好"
if strlen := utf8.RuneCountInString(str); strlen > 500 {
		return error.new('wrong len')
}
// 判断字符 占一个字节
if len(str)
```

### 字符串比较 - 忽略大小写

如果要忽略大小写来比较包含文字数据的字节切片(byte slice)，
不建议使用`bytes`包和`strings`包里的`ToUpper()`、`ToLower()`这些函数转换后再用`==`、`byte.Equal()`、`bytes.Compare()`等比较，`ToUpper()`、`ToLower()`只能处理英文文字，对其它语言无效。因此建议使用`strings.EqualFold()`和`bytes.EqualFold()


 如果要比较用于验证用户数据密钥信息的字节切片时，使用`reflact.DeepEqual()`、`bytes.Equal()`、 `bytes.Compare()`会使应用程序遭受计时攻击(Timing Attack)，可使用`crypto/subtle.ConstantTimeCompare()`避免泄漏时间信息

### struct 类的String()应用

- 正确用法
```
#note
#如果类型定义了 String() 方法，它会被用在 fmt.Printf() 中生成默认的输出：等同于使用格式化描述符 %v 产生的输出。还有 fmt.Print() 和 fmt.Println() 也会自动使用 String() 方法。

package main

import (
	"fmt"
	"strconv"
)

type TwoInts struct {
	a int
	b int
}

func main() {
	two1 := new(TwoInts)
	two1.a = 12
	two1.b = 10
	fmt.Printf("two1 is: %v\n", two1)
	fmt.Println("two1 is:", two1)
	fmt.Printf("two1 is: %T\n", two1)
	fmt.Printf("two1 is: %#v\n", two1)
}

func (tn *TwoInts) String() string {
	return "(" + strconv.Itoa(tn.a) + "/" + strconv.Itoa(tn.b) + ")"
}

输出：

two1 is: (12/10)
two1 is: (12/10)
two1 is: *main.TwoInts
two1 is: &main.TwoInts{a:12, b:10}
```

- 糟糕写法(内存溢出)

```
#note
#不在 String() 方法里调用涉及 String() 方法的方法，会导致意料之外的错误，它导致了一个无限递归调用（TT.String() 调用 fmt.Sprintf，而 fmt.Sprintf 又会反过来调用 TT.String()...），很快就会导致内存溢出

type TT float64

func (t TT) String() string {
    return fmt.Sprintf("%v", t)
}
t.String()
```

