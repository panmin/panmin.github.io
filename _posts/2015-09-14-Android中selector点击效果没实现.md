---
layout:     post                            # 使用的布局（不需要改）
title:      Android中selector点击效果没实现                   # 标题
subtitle:           #副标题
date:       2015-09-14                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中selector点击效果没实现

用两张图片做了个selector，使用ImageView的src或background使用selector点击时，总没出现点击效果，这是为什么呢？经过一番折腾后来才发现一个“秘密”。先不公开，大家来看一看：
```
<?xml version="1.0" encoding="utf-8"?><selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/albumn_upload_normal"/>
    <item android:drawable="@drawable/albumn_upload_selected" android:state_pressed="true"/>
</selector>
```

上面的例子，你们试试，使用时不会出现点击效果。
```
<?xml version="1.0" encoding="utf-8"?><selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/albumn_upload_selected" android:state_pressed="true"/>
    <item android:drawable="@drawable/albumn_upload_normal"/>
</selector>
```
这个例子，会出现点击效果。

总结经验：**default /不点击时的效果图片应该放在最下面，才会有点击效果，不然怎么点都只显示default图片。**