---
layout:     post                            # 使用的布局（不需要改）
title:      Android包含百度地图的Activity完美截图解决方案                  # 标题
subtitle:           #副标题
date:       2015-05-18                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android包含百度地图的Activity完美截图解决方案

1. 第一步：
	在`onCreate`中不使用`setContentView(R.id.main)`来绑定layout文件，改用以下方式：
	```
	View acView = LayoutInflater.from(this).inflate(R.id.main, null);
	acView.setDrawingCacheEnabled(true);
	acView.measure(MeasureSpec.makeMeasureSpe(0,MeasureSpec.UNSPECIFIED),MeasureSpec.makeMeasureSpec(0, MeasureSpec.UNSPECIFIED));
	acView.layout(0, 0, acView.getMeasuredWidth(), acView.getMeasuredHeight());  
	setContentView(acView);
	```
	注意设置acView的几个重要属性。

2. 第二步：
	1、获取当前Activity的截图：
	```
	Bitmap acBitmap = acView.getDrawingCache();
	```
	2、获取当前地图的屏幕截图：
	```
	Bitmap mapBitmap = null;
	mBaiduMap.snapshot(new SnapshotReadyCallback() {
	@Override
	public void onSnapshotReady(Bitmap bitmap) {
          mapBitmap = bitmap;
       }
	});
	```
3. 第三步：合成Bitmap！
	```
	Bitmap newbmp = Bitmap.createBitmap(acBitmap .getWidth(), acBitmap .getHeight(),Config.ARGB_8888);  
    Canvas cv = new Canvas(newbmp);   
    cv.drawBitmap(mapBitmap , 0,X, null);//在 0，X坐标开始画入底层地图的bitmap，X的大小是你Activity标题栏的高度大小，注意，不是手机提示栏的高度大小   
    cv.drawBitmap(acBitmap , 0, 0, null);//在 0，0坐标开始画入上层Activity的bitmap
    cv.save(Canvas.ALL_SAVE_FLAG);//保存   
    cv.restore();//存储
	```
	最终获得newbmp，就是我们要截取的包含地图的activity的bitmap，将它保存到指定位置即可。