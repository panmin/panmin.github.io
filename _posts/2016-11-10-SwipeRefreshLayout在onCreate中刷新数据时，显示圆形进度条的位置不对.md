--
layout:     post                            # 使用的布局（不需要改）
title:      SwipeRefreshLayout在onCreate中刷新数据时，显示圆形进度条的位置不对             # 标题
subtitle:      #副标题
date:       2016-11-10                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


二种方式手动显示下拉动画 
- UI线程列队
```
mSwipeRefreshLayout.post(new Runnable() { 
	@Override 
	public void run() { 
		mSwipeRefreshLayout.setRefreshing(true);
    }
});
```

- 偏移
```
mSwipeRefreshLayout.setProgressViewOffset(false, 0, 100);
mSwipeRefreshLayout.setRefreshing(true);
```
设置mRefreshLayout.setRefreshing(true)只能是控件显示刷新动画，并不能触发setOnRefreshListener事件，这个事件是下拉刷新手势在动画结束时触发的。

参考资料：http://blog.csdn.net/hupei/article/details/51953370

Android利用V4包中的SwipeRefreshLayout实现上拉加载

上拉加载有两种思路：
1、在SwipeRefreshLayout中动态添加和删除子View
2、在listView中中动态添加和删除子View
