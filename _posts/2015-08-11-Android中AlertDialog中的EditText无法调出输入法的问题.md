---
layout:     post                            # 使用的布局（不需要改）
title:      Android中AlertDialog中的EditText无法调出输入法的问题                   # 标题
subtitle:           #副标题
date:       2015-08-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中AlertDialog中的EditText无法调出输入法的问题

```
getWindow().setFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM, WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM);
```

这是默认情况下隐藏软键盘的方法，要重新显示软键盘，要执行下面这段代码：
```
alertDialog.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM);
```
AlertDialog.setView()则不会出现以上问题。

另外：为了防止弹出输入法时把后面的背景挤变形，可以在Manifest里添加：
```
android:windowSoftInputMode="adjustPan|stateHidden"
```