---
layout:     post                            # 使用的布局（不需要改）
title:      HttpURLConnection上传大文件内存溢出                   # 标题
subtitle:            #副标题
date:       2016-12-16                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---

# HttpURLConnection上传大文件内存溢出的原因及解决办法

由于HttpURLConnection默认是有缓存机制的，在对文件操作时，会将读取的数据写入到缓存区中，并不是直接写入到服务器上，只有当流被关闭时，才将数据提交到服务器上。当缓存区的数据大于虚拟机给点的内存时，就导致内存溢出。

解决办法：
**conn.setChunkedStreamingMode(0);**设置该选项，则不使用HttpURLConnection的缓存机制，直接将流提交到服务器上。

