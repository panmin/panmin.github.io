--
layout:     post                            # 使用的布局（不需要改）
title:      ScrollView 嵌套listview自动滑动到底部的解决办法              # 标题
subtitle:   父层加上  android:descendantFocusability="blocksDescendants"   #副标题
date:       2018-01-01                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


### ScrollView 嵌套listview自动滑动到底部的解决办法

ScrollView 嵌套listview时，当listview自动加载时，会自动滑动到scrollview到底部，
**解决方法：**
在listView的父层加上  **android:descendantFocusability="blocksDescendants"** 即可，每次加载都会在scrollview顶部
