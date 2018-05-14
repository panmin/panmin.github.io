--
layout:     post                            # 使用的布局（不需要改）
title:      Android Studio单元测试运行不起来             # 标题
subtitle:      #副标题
date:       2016-06-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



错误log
```
Test running failed: Unable to find instrumentation info for: ComponentInfo{com.yishu.test/android.test.InstrumentationTestRunner}
```

build.gradle中
```
android {
	defaultConfig {
  		multiDexEnabled true
  		testInstrumentationRunner "com.android.test.runner.MultiDexTestRunner"
  	}
}
```