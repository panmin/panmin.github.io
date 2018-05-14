--
layout:     post                            # 使用的布局（不需要改）
title:      Android中绘制view之前测量view的宽高，Android控件getWidth和getHeight返回0             # 标题
subtitle:      #副标题
date:       2016-09-13                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



有时候会发现  用getWidth（）取得的宽度为0，这是ui还未绘制出来就进行了操作，用如下方法可提前取得view的宽高
```
int w = View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED);
int h = View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED);
view.measure(w, h);
int height = view.getMeasuredHeight();
int width = view.getMeasuredWidth();
```