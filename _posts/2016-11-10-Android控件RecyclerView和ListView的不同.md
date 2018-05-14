--
layout:     post                            # 使用的布局（不需要改）
title:      Android控件RecyclerView和ListView的不同             # 标题
subtitle:      #副标题
date:       2016-11-10                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---




1、ListView有选择模式
2、ListView获取可显示区域的index和RecycleView不同
3、ListView有setOnItemClickListener和setOnItemLongClickListener，RecycleView没有
4、ListView能直接设置分割线，RecycleView还需要写ItemAnimator（虽然一般都在item的布局中设置线来当分割线了）


资料：http://www.tuicool.com/articles/aeeaQ3J