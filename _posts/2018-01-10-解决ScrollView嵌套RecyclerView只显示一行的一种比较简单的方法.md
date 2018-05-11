--
layout:     post                            # 使用的布局（不需要改）
title:      解决ScrollView嵌套RecyclerView只显示一行的一种比较简单的方法              # 标题
subtitle:   外层嵌套RelativeLayout   #副标题
date:       2018-01-10                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


**ScrollView嵌套RecyclerView用多了，有些时候就会发现RecyclerView的数据只显示一行。**
```
 <RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:nestedScrollingEnabled="false">
    </android.support.v7.widget.RecyclerView>

</RelativeLayout>
```

**其实只需要在 RecyclerView外层单独加上RelativeLayout或者LinearLayout，RecyclerView就能正常显示**


**只是这个时候滑动冲突可能会导致RecyclerView没有滑动惯性了，就需要给RecyclerView设置属性**
```
//关闭RecyclerView的嵌套滚动效果
android:nestedScrollingEnabled="false"
```