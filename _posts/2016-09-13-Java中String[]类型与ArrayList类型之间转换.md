---
layout:     post                            # 使用的布局（不需要改）
title:      Java中String[]类型与ArrayList类型之间转换                   # 标题
subtitle:   list.toArray(new String[]{}); Arrays.asList(stringArray)  #副标题
date:       2016-09-13                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---


# Java中String[]类型与ArrayList类型之间转换

1. List转为String[]
```
List list = new ArrayList();
String[] stringArray = new String[2]; 
list.toArray(stringArray);    
//String str[] = list.toArray(new String[]{}); 
```

2. String[]转为List
```
String[] stringArray = new String[2]; 
List list = java.util.Arrays.asList(stringArray);
```

3. remove() on List created by Arrays.asList() throws UnsupportedOperationException
```
直接使用Arrays.asList(la)转成List<> 使用remove时会报错，所以可以有一下用法：
List<String> c = new ArrayList<>(Arrays.asList(la));
```