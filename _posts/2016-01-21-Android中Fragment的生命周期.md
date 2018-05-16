---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Fragment的生命周期                  # 标题
subtitle:           #副标题
date:       2016-01-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中Fragment的生命周期

1. 添加到界面上
	```
	FragmentManager manager = getSupportFragmentManager() ;
	FragmentTransaction transaction = manager.beginTransaction() ;
	fragment = new CastMainFragment();
	transaction.add(R.id. frame, fragment );
	transaction.commit() ;
	```
	走了一下生命周期的方法：
	onAttach
	onCreate
	onCreateView
	onStart
	onResume

2. 从界面上移除
	```
	getSupportFragmentManager()
        .beginTransaction()
        .remove(fragment )
        .commit();	
	```
	生命周期：
	onPause
	onStop
	onDestroyView
	onDestroy
	onDetach    