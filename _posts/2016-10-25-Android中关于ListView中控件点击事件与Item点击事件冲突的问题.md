--
layout:     post                            # 使用的布局（不需要改）
title:      Android中关于ListView中控件点击事件与Item点击事件冲突的问题             # 标题
subtitle:      #副标题
date:       2016-10-25                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



**Android中关于ListView中控件点击事件与Item点击事件冲突的问题**

原因是button强制获取了item的焦点，只要设置button的focusable为false即可。