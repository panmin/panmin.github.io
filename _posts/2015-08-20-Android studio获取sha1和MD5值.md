---
layout:     post                            # 使用的布局（不需要改）
title:      Android studio获取sha1和MD5值                   # 标题
subtitle:           #副标题
date:       2015-08-20                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android studio获取sha1和MD5值

提示输入密钥库密码，开发模式默认密码是 android，发布模式的密码是为 apk 的 keystore 设置的密码。输入密钥后回车（如果没设置密码，可直接回车）

```
keytool -list -v -keystore debug.keystore
输入密钥库口令：android（开发模式的默认值是android）
```

![](https://app.yinxiang.com/shard/s14/res/d5055def-0695-4976-a250-5509136fd50e.png)