--
layout:     post                            # 使用的布局（不需要改）
title:      解决Android中ScrollView拉到尽头时再拉的顶部和底部的阴影效果              # 标题
subtitle:   android:fadingEdge=”none”；ScrollView.setHorizontalFadingEdgeEnabled(false)   #副标题
date:       2017-05-18                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


在做项目时采用*RecyclerView*产生*ListView*.和*GridView* 的特效，时发现上下拉取到底部、顶部会带阴影的效果，就感觉不好看。
1. 删除android ScrollView边界阴影方法方法 
	* 在xml中添加：android:fadingEdge=”none” 
	* 代码中添加：ScrollView.setHorizontalFadingEdgeEnabled(false); 
2. 删除ScrollView拉到尽头（顶部、底部），然后继续拉出现的阴影效果 适用于2.3及以上的 否则不用设置 android:overScrollMode="never"