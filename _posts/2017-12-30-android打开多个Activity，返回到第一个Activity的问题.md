--
layout:     post                            # 使用的布局（不需要改）
title:      android打开多个Activity，返回到第一个Activity的问题              # 标题
subtitle:   跳转到第一个activity时，使用FLAG_ACTIVITY_CLEAR_TOP标记   #副标题
date:       2017-12-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

**一、需求**

依次从登录到三级界面，然后退出回到登录界面。

**二、解决办法**

```
//三级界面调用如下代码：
Intent intent = new Intent(context, LoginView.class);
intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
context.startActivity(intent);
```

如果intent对象包含FLAG_ACTIVITY_CLEAR_TOP 标记，当目标task中已存在与接收该intent对象的 activity类型相同的activity实例存在时，所有位于该activity对象上面的activity将被清空，这样接收该intent的 activity就位于栈顶，可以响应到来的intent对象。如果目标activity的运行模式为standard（默认），则目标activtiy也会被清空。因为当运行模式为standard时，总会创建新的activity对象来接收到来的intent对象