---
layout:     post                            # 使用的布局（不需要改）
title:      Android中dp和px之间进行转换                   # 标题
subtitle:           #副标题
date:       2015-09-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中dp和px之间进行转换

在xml布局文件中，我们既可以设置px，也可以设置dp（或者dip）。一般情况下，我们都会选择使用dp，这样可以保证不同屏幕分辨率的机器上布局一致。但是在代码中，如何处理呢？很多控件的方法中都只提供了设置px的方法，例如setPadding，并没有提供设置dp的方法。这个时候，如果需要设置dp的话，就要将dp转换成px了。
以下是一个应用类，方便进行px和dp之间的转换。
```
import android.content.Context;  
  
public class DensityUtil {  
  
    /** 
     * 根据手机的分辨率从 dp 的单位 转成为 px(像素) 
     */  
    public static int dip2px(Context context, float dpValue) {  
        final float scale = context.getResources().getDisplayMetrics().density;  
        return (int) (dpValue * scale + 0.5f);  
    }  
  
    /** 
     * 根据手机的分辨率从 px(像素) 的单位 转成为 dp 
     */  
    public static int px2dip(Context context, float pxValue) {  
        final float scale = context.getResources().getDisplayMetrics().density;  
        return (int) (pxValue / scale + 0.5f);  
    }  
} 
```