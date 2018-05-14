--
layout:     post                            # 使用的布局（不需要改）
title:      Android webView加载h5             # 标题
subtitle:      #副标题
date:       2016-11-28                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


1. 加载方式
	```
	WebView webView = new WebView(getApplicationgContext());//必须传入ApplicationContext如果传入Activity的Context的话，对内存的引用会一直被保持着
	webView.getSettings().setJavaScriptEnabled(true);  //设置WebView属性,运行执行js脚本
	webView.loadUrl("http://www.baidu.com/");          //调用loadView方法为WebView加入链接
	setContentView(webView);                           //调用Activity提供的setContentView将webView显示出来
	```

2. 销毁
	```
	webView.removeAllViews(); 
	webView.destroy();
	```