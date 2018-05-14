--
layout:     post                            # 使用的布局（不需要改）
title:      Android adb logcat输出日志显示不全解决方案             # 标题
subtitle:      #副标题
date:       2016-08-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



在终端中使用adb logcat打印服务器json数据，如果返回数据过大超过4000字节（4K）即会截断不显示

**原因：logcat在对于message的内存分配大概是4k左右.所以超过的内容都直接被丢弃;**

**解决方案：切分超过4k的message，使用多个Log.i输出**

```
public static void showLog(String str) {
    str = str.trim();
    int index = 0;
    int maxLength = 4000;
    String finalString;
    while (index < str.length()) {
        if (str.length() <= index + maxLength) {
            finalString = str.substring(index);
        } else {
            finalString = str.substring(index, maxLength);
        }
        index += maxLength;
        LogHelper.i("str", finalString.trim());
    }
}
```