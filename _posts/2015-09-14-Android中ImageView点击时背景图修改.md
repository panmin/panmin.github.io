---
layout:     post                            # 使用的布局（不需要改）
title:      Android中ImageView点击时背景图修改                   # 标题
subtitle:           #副标题
date:       2015-09-14                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## ImageView点击时背景图修改

记得ImageView要加上加上**clickable="true"**，不然它的selector 是不会有效果。

在使用ImageView时，当被点击时，希望背景图修改一下，这样显示被点击效果明显一些。在这里，一个很简单的方法，最起码是个很清晰的方法。在res/drawable文件夹下创建一个xml文件。比如my.xml,内容如下：
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true"
        android:drawable="@*android:drawable/btn_search_dialog_voice_pressed" />
    <item android:state_pressed="false"
        android:drawable="@*android:drawable/btn_search_dialog_voice_default" />
</selector>
```
这里面要注意一点，就是在上面的drawable是引用的系统的图片资源，如果是使用自己的图片资源，要使用如下格式
```
android:drawable="@drawable/ic_desk_point_normal"
```
然后，在定义imageView的xml文件里面设置：
```
android:src="@drawable/youPicture"
android:background="@drawable/my"
```
以上即可完成效果。