---
layout:     post                            # 使用的布局（不需要改）
title:      Android下拉列表Spinner                  # 标题
subtitle:           #副标题
date:       2015-01-24                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



## Android下拉列表Spinner

使用步骤：
1. 准备数据源
  List<String> list=new ArrayList<String>();
2. 为下拉列表定义一个数据适配器（ArrayAdapter）
  ArrayAdapter adapter=new ArrayAdapter<String>(this,android.R.Layout.Simple_spinner_item,list);
3. 为适配器设置下拉列表下拉时的菜单样式
  adapter.setDropDownViewResource(android.R.Layout.simple_spinner_dropdown_item);
4. 将适配器添加到下拉列表上
  spinner.setAdapter(adapter);
5. 为下拉列表设置各种事件的响应，这个事件响应菜单被选中
  spinner.setOnItemSelectedListener(new Spinner.OnItemSelectedListener(){});