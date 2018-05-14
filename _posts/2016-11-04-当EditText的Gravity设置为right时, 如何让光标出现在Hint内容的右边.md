--
layout:     post                            # 使用的布局（不需要改）
title:      当EditText的Gravity设置为right时, 如何让光标出现在Hint内容的右边             # 标题
subtitle:      #副标题
date:       2016-11-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


```
<com.yishu.views.wedgets.EditText
	  android:id="@+id/tv_remind_title"
	  android:layout_width="match_parent"
	  android:layout_height="match_parent"
	  android:layout_marginRight="@dimen/activity_horizontal_margin"
	  android:inputType="text"
	  android:background="@null"
	  android:layout_alignParentRight="true"
	  android:gravity="center|right"
	  style="@style/editText_singleLine_style"
	  app:maxLength="24"
	  android:textCursorDrawable="@null"
	  android:hint="@string/active_max_char_count"/>
```

关键代码是**android:gravity="center|right"**