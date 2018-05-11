--
layout:     post                            # 使用的布局（不需要改）
title:      ScrollView中Recyclerview显示不全              # 标题
subtitle:   RecycleView添加一个外层布局，或者使用NestedScrollView   #副标题
date:       2018-01-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



## ScrollView中Recyclerview显示不全，在三星note3(5.0系统)可以显示全部，华为(7.0)只显示两条

### 前言

> 项目首页有banner，竖向列表和横向列表等东西，一页肯定显示不全，所以套个了ScrollView。竖向的RecycleView显示的列表却只显示了两条数据

首先考虑是数据源有问题，查看Adapter中，虽然getCount()获取的数量是正确的，是大于两条的，但是onCreateView和OnBindView方法却始终只调用了两次。去掉ScrollView后RecycleView显示数据就能正常显示了。

### 解决方案

RecycleView显示不完全的问题，是和Scrollerview的嵌套产生的，原因是：
1. RecycleView在Scrollerview中重写高度失败，只能显示两个item的高度
2. 重写了LinerLayoutManager的canScrollVertically()方法，禁止了RecycleView的垂直滑动

**解决方法：**
直接给Rcyc外嵌套一个布局，再设置 android:descendantFocusability="blocksDescendants"
让它直接获得焦点（这个属性可以不用加）：
```
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:descendantFocusability="blocksDescendants">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/infor_recycle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="5dp"
        android:background="#FFFFFF">
    </android.support.v7.widget.RecyclerView>
</RelativeLayout>
```


**还有一个解决方案：**
```
把ScrollView换成**NestedScrollView**
```