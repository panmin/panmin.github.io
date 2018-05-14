--
layout:     post                            # 使用的布局（不需要改）
title:      Android多个Fragment切换时重叠             # 标题
subtitle:      #副标题
date:       2016-05-16                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



## 前言

在项目中用到底部导航栏，每个页签都是一个Fragment，在切换时出现了重叠的情况

尝试解决方案：
在项目中需要进行Fragment的切换，一直都是用replace()方法来替换Fragment：
```
public void switchContent(Fragment fragment) {
    if(mContent != fragment) {
        mContent = fragment;
        mFragmentMan.beginTransaction()
            .setCustomAnimations(android.R.anim.fade_in, R.anim.slide_out)
            .replace(R.id.content_frame, fragment) // 替换Fragment，实现切换
            .commit();
    }
}
```

但是，这样会有一个问题：
每次切换的时候，Fragment都会重新实例化，重新加载一边数据，这样非常消耗性能和用户的数据流量。

翻看了Android官方Doc，和一些组件的源代码，发现，replace()这个方法只是在上一个Fragment不再需要时采用的简便方法。

正确的切换方式是`add()`，切换时`hide()`，`add()`另一个Fragment；再次切换时，只需`hide()`当前，`show()`另一个。

这样就能做到多个Fragment切换不重新实例化：
```
public void switchContent(Fragment from, Fragment to) {
    if (mContent != to) {
        mContent = to;
        FragmentTransaction transaction = mFragmentMan.beginTransaction().setCustomAnimations(
                android.R.anim.fade_in, R.anim.slide_out);
        if (!to.isAdded()) {	// 先判断是否被add过
            transaction.hide(from).add(R.id.content_frame, to).commit(); // 隐藏当前的fragment，add下一个到Activity中
        } else {
            transaction.hide(from).show(to).commit(); // 隐藏当前的fragment，显示下一个
        }
    }
}
```

其实是由Activity被回收后重启所导致的Fragment重复创建和重叠的问题。
在Activity `onCreate()`中添加Fragment的时候一定不要忘了检查一下`savedInstanceState`：
```
if (savedInstanceState == null) {
    getFragmentManager().beginTransaction().add(android.R.id.content,
                new UIFragment()).commit();
}
```
为了能准确找出所需的Fragment，所以在`add()`或者`replace()` Fragment的时候记得要带上tag参数，因为一个ViewGroup 容器可以依附`add()`多个Fragment，它们的id自然是相同的。
```
if (savedInstanceState == null) {
    // getFragmentManager().beginTransaction()...commit()
}else{
	//先通过id或者tag找到“复活”的所有UI-Fragment
	UIFragment fragment1 = getFragmentManager().findFragmentById(R.id.fragment1);
	UIFragment fragment2 = getFragmentManager().findFragmentByTag("tag");
	UIFragment fragment3 = ...
	...
	//show()一个即可
	getFragmentManager().beginTransaction()
	        .show(fragment1)
	        .hide(fragment2)
	        .hide(fragment3)
	        .hide(...)
	        .commit();
}
```