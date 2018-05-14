--
layout:     post                            # 使用的布局（不需要改）
title:      Android emulator             # 标题
subtitle:      #副标题
date:       2016-12-23                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



emulator: command not found

解决方法：

重新运行以下命令：
```
source build/envsetup.sh

lunch full-eng

```
之后再次运行 emulator  命令