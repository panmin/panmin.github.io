---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Call requires API level 16 (current min is 8) android.app.xxx                  # 标题
subtitle:           #副标题
date:       2015-04-14                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中Call requires API level 16 (current min is 8) android.app.xxx
一个正常的代码出现这个错误提示：
解决方法：
在工程上右键 Android Tools | Clear Lint Markers

右键点击项目->Android tools ->Clear Link Markers.即可临时解决，但是如果调试用的模拟器是低版本的，则在调试完后还有这个错误。

如果把manifest文件中的user-sdk的android:minSdkVersion改为报错的那个高版本就没事。比如下面：
```
<uses-sdk
	android:minSdkVersion=”11″   //这个之前是8
	android:targetSdkVersion=”17″ />
```
【扩展】

这种错误不仅发生在Level11，也同时发生于其他因为设置了最低版本，但使用了高版本API的代码中，解决方案应该相同。

