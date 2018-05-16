---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Handler如何处理长时间的事件                  # 标题
subtitle:           #副标题
date:       2015-06-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中Handler如何处理长时间的事件

Handler中不能处理长时间的事件，如果需要处理长时间的事件需要以下操作：
1. 在主线程中创建一个Thread来处理长时间的事件；
2. 在Thread中将处理的结果当成message来发送给handler，让handler来更新界面