---
layout:     post                            # 使用的布局（不需要改）
title:      RecyclerView 嵌套RecyclerView 或者 ScrollView当中嵌套RecyclerView ，子View会自动滚动到顶部                   # 标题
subtitle:   子RecycleView焦点被抢占导致  #副标题
date:       2018-01-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


此问题一开始我以为是我在fragment当中，返回和移动的时候调用了adpater的notifyItemChange方法情况和notifyItemChanged(int position) 引起的返回时RecyclerView页面不自然的跳动 一样，但是发现我并没有调用过此方法，所以我想是不是有可能是子 recyclerView 抢了焦点导致子 RecyclerView自动滚动到了第一行，所以我在子recyclerView设置

通过xml设置该属性不管用，这样好用（不知道为什么）
```
recycler_mine_partake_message.setFocusableInTouchMode(false); //设置不需要焦点
```

或者在父RecycleView或者ScrollView的xml中设置：
```
android:descendantFocusability="blocksDescendants"
```

descendantFocusability有3中属性：
* beforeDescendants：viewgroup会优先其子类控件而获取到焦点
* afterDescendants：viewgroup只有当其子类控件不需要获取焦点时才获取焦点
* blocksDescendants：viewgroup会覆盖子类控件而直接获得焦点