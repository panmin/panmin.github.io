---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Fragment的使用                  # 标题
subtitle:           #副标题
date:       2015-04-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中Fragment的使用

1. 重新`onCreateView`,用`inflater.inflate(R.layout. stores, container)`;来加载view，这一点和在Activity中直接`setContentView(R.layout.stores)`;不同
2. 如何获取上下文**Context**:`context = getActivity();`
3. 整个Fragment看成是一个ViewGroup容器，从onCreateView中获得。