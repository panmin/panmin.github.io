--
layout:     post                            # 使用的布局（不需要改）
title:      Android中RatingBar自定义大小,自定义样式              # 标题
subtitle:      #副标题
date:       2017-04-13                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

1. **首先声明自定义RatingBar的样式(values/styles.xml)**

	```
	<style name="RadingStyle" parent="@android:style/Widget.RatingBar">
	    <!-- 定义星星图片 -->
	    <item name="android:progressDrawable">@drawable/rating_bar</item>
	    <!-- 根据自定义星星图片的大小,设置相应的值,否则可能显示不全 -->
	    <item name="android:minHeight">80dp</item>
	</style>
	```

2. **自定义RatingBar的图片(drawable/rating_bar.xml)**
	
	```
	<?xml version="1.0" encoding="utf-8"?>
	<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
	<!-- 必须使用此id,drawable为 未选中 状态的图片 -->
	   <item
	       android:id="@+android:id/background"
	       android:drawable="@drawable/rating_bar_1"/>
	<!-- 必须使用此id,drawable为 半选中 状态的图片 -->
	   <item
	       android:id="@+android:id/secondaryProgress"
	       android:drawable="@drawable/rating_bar_4"/>
	<!-- 必须使用此id,drawable为 全选中 状态的图片 -->
	   <item
	       android:id="@+android:id/progress"
	       android:drawable="@drawable/rating_bar_6"/>
	</layer-list>
	```
3. **布局文件中,引用即可(layout/layout.xml)**

	```
	<RatingBar
	    style="@style/RadingStyle" <!-- 注意此处 -->
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:numStars="5" <!-- 星星总数 -->
	    android:rating="3" <!-- 默认选中数 -->
	    android:stepSize="0.5" <!-- 步长数 -->
	    />
	```