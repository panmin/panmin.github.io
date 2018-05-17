---
layout:     post                            # 使用的布局（不需要改）
title:      Android Studio编译报错GC overhead limit exceeded      # 标题
subtitle:   内存不足，添加脚本指定编译堆内存大小         #副标题
date:       2018-04-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

#### Android Studio编译报错“java.lang.OutOfMemoryError: GC overhead limit exceeded” 

1. 在build.gradle添加脚本指定编译堆内存
	
	* 如果在整个工程中生效，则在build.gradle中增加如下配置：
	```
	android {
		dexOptions {
        	incremental true
        	javaMaxHeapSize "4g"
    	}
	}
	```
 
	* 如果只在单元测试的时候生效，则在build.gradle中增加如下配置:
	```
	android {
		testOptions {
			android.dexOptions {
	        	incremental true
	        	javaMaxHeapSize "4g"
	    	}
		}
	}
	```
2. 添加gradle的配置文件指定堆内存

	也可以在gradle.properties中修改build环境中的jvmargs参数大小：
	在项目根目录新建gradle.properties文件内容为：
	```
	org.gradle.daemon=true
	org.gradle.parallel=true
	org.gradle.jvmargs=-Xmx5120m
	```