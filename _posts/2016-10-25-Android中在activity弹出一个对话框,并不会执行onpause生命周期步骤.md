--
layout:     post                            # 使用的布局（不需要改）
title:      Android中在activity弹出一个对话框,并不会执行onpause生命周期步骤             # 标题
subtitle:      #副标题
date:       2016-10-25                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



android 中在activity弹出一个对话框,并不会执行onpause生命周期方法 
在实际的应用开发中,我们可能会在onResume中进行网络交互,在网络交互的过程中免不了使用Progressdialog,那么现在问题来了,在我们弹出一个进度对话框的时候如果我们的activity的onpause生命方法得到执行,然后在数据加载完毕,对话框进行消失的时候,activity的onResume再次得到执行,然后再次请求网络,这样一个恶性循环就开始了,这样的错误实在是太严重了,当然了上面只是在我的想象之中,然后就用代码实验了下,在弹出一个对话框的时候我们activity的onpause并没有执行,所以也就不会导致onResume重复执行了,
在我们创建对话框的时候,需要传入一个context类型的参数.这个参数也就说明我们的dialog是在这个上下文创建的,我们的activity也是正在交互的,并不会执行onPause的