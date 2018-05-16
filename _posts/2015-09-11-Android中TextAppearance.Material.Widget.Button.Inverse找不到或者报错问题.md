---
layout:     post                            # 使用的布局（不需要改）
title:      Android中TextAppearance.Material.Widget.Button.Inverse找不到或者报错问题                   # 标题
subtitle:           #副标题
date:       2015-09-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中TextAppearance.Material.Widget.Button.Inverse找不到或者报错问题
前两天将 `android sdk` 升到 `android6.0` 后出现 `Error retrieving parent for Item - AppCompact-v7 23`
或者无法解析 `android:TextAppearance.Material.Widget.Button.Inverse` 和 `android:Widget.Material.Button.Colored` 找不到

解决办法有两种：
    其一：将android6.0所涉及到的appcompat-v7和sdk全部下载（需要翻墙），但是下载好后在主目录中好多类库不能用，想httppost等网络方面的库（不建议此方法）
    其二：找到你的sdk目录下的android-sdk-windows\extras\android\m2repository\com\android\support\appcompat-v7下的23.0.0全部删除，然后重新建立新project就没有什么问题啦，当然在android6.0建的项目依然不能用的！