---
layout:     post                            # 使用的布局（不需要改）
title:      Android经典蓝牙connect时java.io.IOException                   # 标题
subtitle:            #副标题
date:       2015-09-18                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android经典蓝牙connect时java.io.IOException: read failed, socket might closed or timeout, read ret: -1错误

造成这个错误的原因的是**对方的蓝牙没有accept**