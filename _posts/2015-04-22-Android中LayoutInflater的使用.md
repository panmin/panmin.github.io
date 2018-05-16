---
layout:     post                            # 使用的布局（不需要改）
title:      Android中LayoutInflater的使用                  # 标题
subtitle:           #副标题
date:       2015-04-22                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中LayoutInflater的使用

第一种方法：
```
LayoutInflater inflater = LayoutInflater.from(this);  
View layout = inflater.inflate(R.layout.main, null);  
```

第二种方法：
```
LayoutInflater inflater = getLayoutInflater();  
View layout = inflater.inflate(R.layout.main, null); 
```

第三种方法：
```
LayoutInflater inflater = (LayoutInflater) getSystemService(LAYOUT_INFLATER_SERVICE);  
View layout = inflater.inflate(R.layout.main, null);  
```

