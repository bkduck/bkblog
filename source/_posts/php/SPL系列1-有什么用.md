---
title: SPL语法系列1——有什么用
excerpt: SPL的目的是实现一些有效的数据访问接口和类的PHP。功能是用来遍历聚合数据结构(任何你想要遍历)。这些可能包括数组、数据库结果集,xml树、目录或者任何list！
index_img: https://static.bkduck.cn/img/blog/php-spl-202109281636.png
date: 2021-03-05
updated: 2021-03-05
categories:
  - PHP 
tags:
  - php
  - php语法 
---

## What SPL简介  

**SPL全称**：Standard PHP Library (标准php类库)。 

引用phpro.org站点的解析：The aim of SPL is to implement some efficient data access interfaces and classes for PHP. Functionally it is designed to traverse aggregate structures (anything you want to loop over). These may include arrays, database result sets, xml trees, directory listings or any list at all.  

**原文翻译**：SPL的目的是实现一些有效的数据访问接口和类的PHP。功能是用来遍历聚合数据结构(任何你想要遍历)。这些可能包括数组、数据库结果集,xml树、目录或者任何list！

**个人心得**：  

- SPL主要对外提供统一，便捷，快速的遍历方式（++一般不能foreach遍历对象Object++）
- SPL已经编写好了基础的structures（数据结构），如SplDoublyLinkedList（双链表）、SplStack（栈）等 
- SPL核心是Iterrator（迭代器），同时SPL提供很多Iterator，如RecursiveArrayIterator（递归数组迭代器）、ArrayIterator（数组迭代器）DirectoryIterator(目录迭代器) 等   
- SPL 主要组成：数据结构、迭代器、•接口、•异常、SPL 函数、文件处理、各种类及接口

## How 如何学

也许很多人常听SPL，但大概入门找不到方向，其实学习无外乎‘3W’（what、why、how）bkduck将围绕这3w做一个系列的记录，方便以后自己查阅 



