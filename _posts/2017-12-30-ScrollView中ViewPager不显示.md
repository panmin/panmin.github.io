--
layout:     post                            # 使用的布局（不需要改）
title:      ScrollView中ViewPager不显示              # 标题
subtitle:   跳转到第一个activity时，使用FLAG_ACTIVITY_CLEAR_TOP标记   #副标题
date:       2017-12-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


ScrollView中ViewPager不显示，需要重写ViewPager，还有自动滑动到底部，要设置ScrollView下第一个子元素的android:focusableInTouchMode="true"

```
<ScrollView
    android:layout_width="match_parent"
    android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:focusableInTouchMode="true">
            <ImageView
                android:id="@+id/iv"
                android:layout_width="match_parent"
                android:layout_height="180dp"
                android:src="@drawable/ph3"/>
            <android.support.design.widget.TabLayout
                android:id="@+id/tab"
                android:layout_width="match_parent"
                android:layout_height="@dimen/dimen_48dp"
                app:tabTextColor="@color/default_fontColor"
                app:tabSelectedTextColor="@color/red"
                android:background="@color/bg"
                app:tabTextAppearance="@android:style/TextAppearance.Holo.Small"
                app:tabBackground="@drawable/tab_header_selector1"
                app:tabIndicatorColor="@android:color/transparent"
                app:tabMode="scrollable"/>
            <com.jiangxinkaiwu.views.widgets.CustomViewPager
                android:id="@+id/vp"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"/>
        </LinearLayout>
</ScrollView>
```