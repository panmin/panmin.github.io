---
layout:     post                            # 使用的布局（不需要改）
title:      Android中asset文件夹和raw文件夹区别                  # 标题
subtitle:           #副标题
date:       2015-09-07                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



## Android中asset文件夹和raw文件夹区别


### res/raw和assets的相同点：
两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。


### res/raw和assets的不同点：
1. res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。
2. res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹


**读取文件资源：**
1. 读取res/raw下的文件资源，通过以下方式获取输入流来进行写操作
	```
	InputStream is =getResources().openRawResource(R.id.filename); 
	```
2. 读取assets下的文件资源，通过以下方式获取输入流来进行写操作
	```
	AssetManager am = null;  
    am = getAssets();  
    InputStream is = am.open("filename");  
	```


注意1：Google的Android系统处理Assert有个bug，在**AssertManager中不能处理单个超过1MB的文件**，不然会报异常，**raw**没这个限制可以放个**4MB**的Mp3文件没问题。

注意2：assets 文件夹是存放不进行编译加工的原生文件，即该文件夹里面的文件不会像 xml， java 文件被预编译，可以存放一些图片，html，js, css 等文件。