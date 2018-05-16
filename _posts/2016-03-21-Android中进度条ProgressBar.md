---
layout:     post                            # 使用的布局（不需要改）
title:      Android中进度条ProgressBar                  # 标题
subtitle:           #副标题
date:       2016-03-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中进度条ProgressBar

1. Android自带的ProgressBar显示风格
	```
    style="?android:attr/progressBarStyleLarge"  大环形进度条
    style="?android:attr/progresssBarStyleSmall"  小环形进度条
    style="?android:attr/progressBarStyleHorizontal"  水平进度条
	```
2. ProcessBar的分类
     可以精确显示精度（可以显示刻度或者百分比）
     不可以精确显示进度的（类似与过场动画，一直转啊转）