---
layout:     post                            # 使用的布局（不需要改）
title:      Android使用selector修改TextView中字体的颜色                   # 标题
subtitle:           #副标题
date:       2015-09-15                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android使用selector修改TextView中字体的颜色

我们在TextView中设置字体颜色一般使用 
```
android:textColor="@color/red"
```
但是我们在使用selector动态修改字体颜色的时候要使用

我遇到这个问题的时候是在TabActivity中，每个Tab在选中的时候修改为蓝色。
tab_item.xml的代码如下：
```
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    android:id="@+id/ll_software_tabwidget_item"  
    android:layout_width="fill_parent"  
    android:layout_height="fill_parent"  
    android:gravity="center_horizontal"  
    android:orientation="vertical" >  
  
   <ImageView  
        android:id="@+id/iv_software_tabwidget_icon"  
        android:layout_width="30dip"  
        android:layout_height="30dip"  
        android:layout_marginBottom="1dip"  
        android:layout_marginTop="5dip"  
        android:scaleType="fitXY" />  
  
    <TextView  
        android:id="@+id/tv_software_tabwidget_text"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_marginBottom="5dip"  
        android:textColor="@drawable/software_textcolor"  
        android:textSize="14dip" />  
      
</LinearLayout>  
```

注意`android:textColor="@drawable/software_textcolor"`，即software_textcolor.xml就是selector，源码如下：

```
<?xml version="1.0" encoding="utf-8"?>  
<selector xmlns:android="http://schemas.android.com/apk/res/android" >  
    <item android:state_selected="true" android:color="@color/software_textColor_selected"></item>  
    <item android:state_selected="false" android:color="@color/software_textColor_unselected"></item> 
</selector>  
```

这个文件中就是要注意的地方了，必须使用**android:color**="@color/software_textColor_selected"，**不能使用android:textColor属性**。


另附color.xml的源码如下：
```
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <color name="software_textColor_selected">#FF1C94EA</color>  
    <color name="software_textColor_unselected">#FFDCE0DF</color>  
</resources>  
```


