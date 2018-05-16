---
layout:     post                            # 使用的布局（不需要改）
title:      Android中setWidth()和setHeight()没反应的问题                   # 标题
subtitle:           #副标题
date:       2015-09-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android setWidth()和setHeight()没反应的问题


editText=(EditText)findViewById(R.id.myEditText);
// editText.setHeight(10); //不生效
editText.**getLayoutParams().height** = 100; //这样设置生效