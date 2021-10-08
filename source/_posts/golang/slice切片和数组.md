---
title: Golang语法系列——slice切片和数组 
excerpt: 切片(slice)是 Golang 中一种比较特殊的数据结构，这种数据结构更便于使用和管理数据集合。切片是围绕动态数组的概念构建的，可以按需自动增长和缩小。切片的动态增长是通过内置函数 append() 来实现的，这个函数可以快速且高效地增长切片，也可以通过对切片再次切割，缩小一个切片的大小。因为切片的底层也是在连续的内存块中分配的，所以切片还能获得索引、迭代以及为垃圾回收优化的好处。
index_img: https://static.bkduck.cn/img/blog/go-202109281111.png
date: 2021-08-02
updated: 2021-08-02
categories:
  - Golang
tags:
  - golang
  - go语法 
---


## slice切片底层

> 基于数组实现，可动态拓容，

### 切片拓容规则 

**append()** 在分配内存空间之前需要先确定新的切片容量，运行时根据切片的当前容量选择不同的策略进行扩容

* 如果期望容量大于当前容量的两倍就会使用期望容量；

* 如果当前切片的长度小于 1024 就会将容量翻倍；
* 如果当前切片的长度大于 1024 就会每次增加 25% 的容量，直到新容量大于期望容量；

---

**copy**拷贝，[`runtime.memmove`](https://draveness.me/golang/tree/runtime.memmove) 能够提供更好的性能。需要注意的是，整块拷贝内存仍然会占用非常多的资源，在大切片上执行拷贝操作时一定要注意对性能的影响

---

**总结** 

1. append 注意拓容规则，初始化时可规划好长度
2. copy 避免大切片拷贝



## slice使用

### slice cap分析

**copy() 是值赋值**，不会影响原切片cap，同时基于数组的新切片，修改新切片值，会覆盖数组的值。

```go
  
  
  arr := [10]int64{1,2,3,4,5,6,7,8,9,10}
	s1 := arr[2:4:8] 	// cap=6,len=2 cap 针对源数组 arr 由2开始
	s2 := arr[2:4] 		// cap=8,len=2
  s3 := arrp[:4] 		// cap=10,len=4 
  //s1[0] = 4 // arr: [1,2,4,4,5,6,7,8,9,10] //切片指向arr地址，修改会覆盖
  //cp := []int64{22,33}
  //copy(s1,cp) //arr: [1,2,22,33,5,6,7,8,9,10] copy是值复制，不影响s1的cap
	
	fmt.Println(s1) //[3 4]
	fmt.Println(s2) //[3 4]
	fmt.Println(s1[:cap(s1)]) //[3 4 5 6 7 8]
	fmt.Println(cap(s1)) // 6 8-2
	fmt.Println(len(s1)) // 2
	fmt.Println(s2[:cap(s2)])//[3 4 5 6 7 8 9 10]
	fmt.Println(cap(s2)) // 8  10-2
	fmt.Println(len(s2)) // 2

  
```

### Slice增加元素重新分配内存

slice在添加元素前，与其它切片共享同一数据区域，修改会相互影响；但添加元素导致内存重新分配之后，不再指向原来的数据区域，修改元素，不再影响其它切片。

```go
    s1 := []int{1,2,3}
    fmt.Println(len(s1),cap(s1),s1) //prints 3 3 [1 2 3]

    s2 := s1[1:]
    fmt.Println(len(s2),cap(s2),s2) //prints 2 2 [2 3]

    for i := range s2 { s2[i] += 20 }

    //still referencing the same array
    fmt.Println(s1) //prints [1 22 23]
    fmt.Println(s2) //prints [22 23]

    s2 = append(s2,4)

    for i := range s2 { s2[i] += 10 }

    //s1 is now "stale"
    fmt.Println(s1) //prints [1 22 23]
    fmt.Println(s2) //prints [32 33 14]
```



## 参考链接

* [切片实现原理](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-array-and-slice/)

