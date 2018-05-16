---
layout:     post                            # 使用的布局（不需要改）
title:      Android中AsyncTask和Handler的优缺点                  # 标题
subtitle:           #副标题
date:       2015-04-27                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



## Android中AsyncTask和Handler的优缺点

### AsyncTask
是android提供的轻量级的异步类，可以直接继承AsyncTask，在类中实现异步操作，并提供接口反馈当前异步执行的程度（可以通过接口实现UI进度的更新），最后反馈执行的结果给UI主线程、
     
优点：简单、快捷、过程可控
缺点：在使用多个异步操作和并需要进行UI变更时，就变得复杂了。比如更新UI特别是大量的itemview的时候各种不给力。

### Handler
在Handler异步实现时，涉及到Handler、Looper、Message、Thread四个对象，实现异步的流程是主线程启动Thread运行并生成Message-Looper获取Message并传递给Handler，Handler逐个获取Looper中的Message，并进行UI变更。
     
优点：结构清晰，功能定义明确，对于多个后台任务时简单清晰
缺点：在单个后台异步处理时，代码显得过多，结构相对复杂