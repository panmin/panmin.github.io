--
layout:     post                            # 使用的布局（不需要改）
title:      Android中notifyDataSetInvalidated()和notifyDataSetChanged()有什么区别             # 标题
subtitle:      #副标题
date:       2016-11-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


![](http://hi.csdn.net/attachment/201112/31/0_1325331578V4M3.gif)

**public void notifyDataSetChanged ()**

> 该方法内部实现了在每个观察者上面调用onChanged事件。每当发现数据集有改变的情况，或者读取到数据的新状态时，就会调用此方法。


**public void notifyDataSetInvalidated ()**

> 该方法内部实现了在每个观察者上面调用onInvalidated事件。每当发现数据集监控有改变的情况，就会调用此方法。


通俗一下从字面上面理解，就是
      1. 如果要刷新的数据源改变了就调用`notifyDataSetChanged()`例如:我们在新浪微博中，要进行分显示刷新微博;
      2. 如果那个数据源失效了之后就 调用`notifyDataSetInvalidated()`