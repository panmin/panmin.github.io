--
layout:     post                            # 使用的布局（不需要改）
title:      Android setTextSize             # 标题
subtitle:      #副标题
date:       2016-11-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---





```
<dimen name="font_14">14sp</dimen>

tvToday.setTextColor(getResources().getColor(R.color.font_gray));
tvToday.setTextSize(TypedValue.COMPLEX_UNIT_PX,getResources().getDimension(R.dimen.font_14));
```
这样设置才会和在xml中设置**android:textSize="@dimen/font_14"**效果才会一样。



使用如下代码时，发现字号不会变大，反而会变小：
```
size = (int) mText.getTextSize() + 1;
mText.setTextSize(size);
```
后来发现getTextSize返回值是以像素(px)为单位的，而setTextSize()是以sp为单位的，两者单位不一致才造成这样的结果。


这里可以用setTextSize()的另外一种形式，可以指定单位：
```
setTextSize(int unit, int size)
TypedValue.COMPLEX_UNIT_PX : Pixels
TypedValue.COMPLEX_UNIT_SP : Scaled Pixels
TypedValue.COMPLEX_UNIT_DIP : Device Independent Pixels
```

下面这样就正常了：
```
size = (int) mText.getTextSize() + 1;
mText.setTextSize(TypedValue.COMPLEX_UNIT_PX, size);
```