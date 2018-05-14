--
layout:     post                            # 使用的布局（不需要改）
title:      Android window 一些属性说明             # 标题
subtitle:      #副标题
date:       2016-11-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---




```
<item name="android:windowFrame">@null</item> ：Dialog的windowFrame框为无
<item name="android:windowIsFloating">true</item>：是否浮现在activity之上
<item name="android:windowIsTranslucent">false</item>:是否半透明
<item name="android:windowNoTitle">true</item>:是否显示title
<item name="android:windowBackground">@drawable/dia_bg</item>:设置dialog的背景
<item name="android:backgroundDimEnabled">false</item>: 背景是否变暗
```

```
<style name="MyDialogStyle">
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowFrame">@null</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowIsFloating">true</item>
    <item name="android:windowIsTranslucent">true</item>
    <item name="android:windowContentOverlay">@null</item>
    <item name="android:windowAnimationStyle">@android:style/Animation.Dialog</item>
    <item name="android:backgroundDimEnabled">true</item>
</style>
```


