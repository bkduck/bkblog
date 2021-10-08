---
title: golang基础语法-interface接口 
excerpt: 如果说goroutine和channel是Go并发的两大基石，那么接口是Go语言编程中数据类型的关键。在Go语言的实际编程中，几乎所有的数据结构都围绕接口展开，接口是Go语言中所有数据结构的核心
date: 2021-08-05
updated: 2021-08-05
categories:
  - Golang
tags:
  - golang
  - go基础语法  
sticky: 100
index_img: /img/example.jpg
banner_img: /img/post_banner.jpg
comment: 'valine'
hide: true
---

## 字段解析
`title`: 文章标题

`excerpt`: 手动文章摘要

`categories`: 文章分类

`hide`: 隐藏文章

`sticky`: 文章排序权重，数值越大，该文章越靠前

当文章设置了 sticky 后，主题会默认在首页文章标题前增加一个图标，来标识这是一个置顶文章，你可以通过主题配置去关闭或修改这个功能：
```yaml
index:
  post_sticky:
    enable: true
    icon: 'iconfont icon-top'
```

`index_img`: 文章首页封面图

/img/example.jpg 对应的是存放在 /source/img/example.jpg

如果想统一给文章设置一个默认图片（文章不设置 index_img 则默认使用这张图片），可在主题配置中设置:
```yaml
post:
  default_index_img: /img/example.jpg
```

`banner_img`: 文章顶部大图

`comment`: 文章评论 [^1]

如果想在某个文章页关闭评论，或者想在某个自定义页面开启评论，可以通过在 Front-matter (opens new window)设置 comment: bool 来控制评论开关，或者通过 comment: 'type' 来开启指定的评论插件。


## 参考
[^1]: 参考资料1
[^2]: 参考资料2