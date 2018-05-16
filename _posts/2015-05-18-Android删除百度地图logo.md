---
layout:     post                            # 使用的布局（不需要改）
title:      Android删除百度地图logo                  # 标题
subtitle:           #副标题
date:       2015-05-18                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android删除百度地图logo

**百度logo的index是1**
**zoom的index是2**
**比例尺的index是3**

```
/**
* 删除百度地图logo
* */
vcBmapView.removeViewAt(2); 

/**
* 删除百度地图logo和缩放控件
* */
int count = vcBmapView.getChildCount();
for (int i = 0; i < count; i++) {
    View child = vcBmapView.getChildAt(i);
    if (child instanceof ImageView || child instanceof ZoomControls) {
        // 百度地图logo是ImageView的实例，缩放控件是ZoomControls的实例
        child.setVisibility(View.GONE);
    }
}
```