---
layout:     post                            # 使用的布局（不需要改）
title:      Android中警告信息“Nested weights are bad for performance”的消除方法                  # 标题
subtitle:           #副标题
date:       2015-04-09                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## 警告信息“Nested weights are bad for performance”的消除方法

原因分析：在布局进行嵌套使用时，父布局与子布局都使用了android:layout_weight，但不是必须使用时，便会出现如题所示的警告信息。

解决方法：根据实际情况，去除子布局中非必须使用的android:layout_weight。