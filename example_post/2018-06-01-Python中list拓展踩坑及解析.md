---
title: "Python中list拓展踩坑及解析"
layout: post
date: 2018-06-01 11:01
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Python
- List
- Solution
category: blog
author: ethan
description: 对于python中+=，append,extend的分析

---

## Python List的拓展操作

在使用python list 的时候，经常会使用对于列表的拓展操作，因为刷Leetcode中遇到了+=与append的一些坑，简易解析下三个拓展操作。


对于list的+=，它要求右边对象为iterable的，能够迭代的，例如元组、列表及字典等。
而对于append,其参数为 **obj--添加到列表末尾的对象**，无返回值，但是会修改原来的列表。对于extend，其参数为**seq -- 元素列表**，该方法没有返回值，但会在已存在的列表中添加新的列表内容。 三种操作都没有去重功能。

extend与+=的要求一样，要求参数是iterable的。

而且元素在+=操作后添加逗号和不添加逗号是不一样的。前者是元组，后者不是。

$ highlight python $
3, #元组
(3,) #元组
(3) #int
$ endhighlight $ 	



