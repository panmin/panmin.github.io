---
layout:     post                            # 使用的布局（不需要改）
title:      Unsupported Modules Detected              # 标题
subtitle:   点击Sync Project With Gradle Files就解决问题了   #副标题
date:       2018-01-24                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


```
Unsupported Modules Detected: Compilation is not supported for following modules: lib_DragViewPager, lib_commonUtils, lib_dateTimePickers, lib_fileSelector, lib_weekMonth. Unfortunately you can't have non-Gradle Java modules and Android-Gradle modules in one project.

Plugin Error  
            Problems found loading plugins:  
            Following plugins are incompatible with current IDE build: .ignore 
```

在更新aar的时候，把项目搞坏了，报了一个这样的错误， 然后就是各种倒腾，clean， rebuild， 重启，没屌用，最后好像看好像是Gradle的问题。


**点击Sync Project With Gradle Files就解决问题了**