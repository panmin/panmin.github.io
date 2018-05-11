---
layout:     post                            # 使用的布局（不需要改）
title:      Android studio创建project时出现报错Write access is allowed from event dispatch thread only     # 标题
subtitle:   设置Android studio自带jre有问题，要重选JDK路径    #副标题
date:       2018-02-23                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

#### Android studio创建project时出现报错：“Gradle error : Write access is allowed from event dispatch thread only in android studio ”

 今天创建project时在MainActivity.java文件出现报错：”Gradle error : Write access is allowed from event dispatch thread only in android studio "。找了好久没找到解决办法。后来在百度时发现是sdk的问题，在路径处设置Android studio自带的jre，勾选Use embedded JDK(recommended)就好了。

```
Error:Failed to complete Gradle execution.
Cause:
Write access is allowed from event dispatch thread only
```