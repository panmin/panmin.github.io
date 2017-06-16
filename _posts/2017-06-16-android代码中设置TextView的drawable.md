---
layout:     post                            # 使用的布局（不需要改）
title:      android代码中设置TextView的drawable                   # 标题
subtitle:            #副标题
date:       2017-06-16                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - android
---

# android代码中设置TextView的drawable

```
Drawable drawable= getResources().getDrawable(R.drawable.drawable);
// 这一步必须要做,否则不会显示.
drawable.setBounds(0, 0, drawable.getMinimumWidth(), drawable.getMinimumHeight());
myTextview.setCompoundDrawables(drawable,null,null,null);
```

以上代码等效于：
```
myTextview.setCompoundDrawablesWithIntrinsicBounds (Drawable left,
Drawable top, Drawable right, Drawable bottom)
```