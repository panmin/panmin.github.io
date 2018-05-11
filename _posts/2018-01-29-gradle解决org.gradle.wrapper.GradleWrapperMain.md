---
layout:     post                            # 使用的布局（不需要改）
title:      gradle解决org.gradle.wrapper.GradleWrapperMain                   # 标题
subtitle:   缺少wrapper文件夹   #副标题
date:       2018-01-29                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



#### java.lang.ClassNotFoundException: org.gradle.wrapper.GradleWrapperMain

> 【错误: 找不到或无法加载主类 org.gradle.wrapper.GradleWrapperMain】

1. 从一个ok的项目里考了份wrapper过来（实际就一个jar和一个配置文件）
2. 重新在 as 的项目根路径下运行【gradlew :yourAndroidModlueFolderName:aB】
ok...