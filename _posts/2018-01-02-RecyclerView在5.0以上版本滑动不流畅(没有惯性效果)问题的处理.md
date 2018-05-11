--
layout:     post                            # 使用的布局（不需要改）
title:      RecyclerView在5.0以上版本滑动不流畅(没有惯性效果)问题的处理              # 标题
subtitle:   需要设置setNestedScrollingEnabled(false)   #副标题
date:       2018-01-02                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



项目中发现，RecyclerView在5.0以上版本滑动不流畅,滑动的手指一离开屏幕，列表就不动了，没有惯性效果。调查原因，发现应该是与scroll bar的有关，据说是recyclerView 无法完美support scroll。
        
在网上找了找解决方案，有人说把targetSDK改成21可以解决问题，但是并不好用。
既然recyclerView无法完美支持scroll，那么试着在xml布局文件中，在recyclerView外面再套一层scrollView，问题解决！
好景不长，发现在一个需要上拉加载和下拉刷新的页面中，这个问题依然存在(刷新组件是自己做的linearLayout,里面维护了一个scrollView，并自己根据坐标分发拦截touch事件）。猜想应该是事件冲突所致。
发现为recyclerView设置禁止嵌套滑动
```
setNestedScrollingEnabled(false);  
```