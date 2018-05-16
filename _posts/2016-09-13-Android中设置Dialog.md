---
layout:     post                            # 使用的布局（不需要改）
title:      Android中设置Dialog                  # 标题
subtitle:           #副标题
date:       2016-09-13                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中设置Dialog

1. 设置Dialog没有title
	```
	Dialog d = new Dialog(context);
	d.requestWindowFeature(Window.FEATURE_NO_TITLE);
	```
2. 设置Dialog在界面上的坐标
	```
	mUserNameDialog = new Dialog(this);
	mUserNameDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
	mUserNameDialog.setContentView(popupWindow);
	Window window = mUserNameDialog.getWindow();
	/*
     * lp.x与lp.y表示相对于原始位置的偏移.
     * 当参数值包含Gravity.LEFT时,对话框出现在左边,所以lp.x就表示相对左边的偏移,负值忽略.
     * 当参数值包含Gravity.RIGHT时,对话框出现在右边,所以lp.x就表示相对右边的偏移,负值忽略.
     * 当参数值包含Gravity.TOP时,对话框出现在上边,所以lp.y就表示相对上边的偏移,负值忽略.
     * 当参数值包含Gravity.BOTTOM时,对话框出现在下边,所以lp.y就表示相对下边的偏移,负值忽略.
     * 当参数值包含Gravity.CENTER_HORIZONTAL时
     * ,对话框水平居中,所以lp.x就表示在水平居中的位置移动lp.x像素,正值向右移动,负值向左移动.
     * 当参数值包含Gravity.CENTER_VERTICAL时
     * ,对话框垂直居中,所以lp.y就表示在垂直居中的位置移动lp.y像素,正值向右移动,负值向左移动.
     * gravity的默认值为Gravity.CENTER,即Gravity.CENTER_HORIZONTAL |
     * Gravity.CENTER_VERTICAL.
     * 
     * 本来setGravity的参数值为Gravity.LEFT | Gravity.TOP时对话框应出现在程序的左上角,但在
     * 我手机上测试时发现距左边与上边都有一小段距离,而且垂直坐标把程序标题栏也计算在内了,
     * Gravity.LEFT, Gravity.TOP, Gravity.BOTTOM与Gravity.RIGHT都是如此,据边界有一小段距离
     */
	window.setGravity(Gravity.LEFT | Gravity.TOP);
	int[] location = new int[2];
	etusername.getLocationInWindow(location);//location[1]获取的是控件的中间部分在界面上的坐标（就是加上了高度的二分之一）
	WindowManager.LayoutParams attributes = window.getAttributes();
	attributes.x = location[0];
	attributes.y = location[1]+etusername.getHeight()/2;
	attributes.width = etusername.getWidth();
	window.clearFlags( WindowManager.LayoutParams.FLAG_DIM_BEHIND);//消除Dialog内容区域外围的灰色
	```

	重要的两点就是：

  （1）如果你需要设置x的值，那么需要将gravity设置有LEFT,START,RIGHT或者END。

  （2）如果你需要设置y的值，那么需要将gravity设置成有TOP,BOTTOM。

	上面的两点，如果你的对FrameLayout设置的margin不起作用了，可能也是需要遵守上面两点规则。

3. 消除Dialog内容区域外围的灰色
	```
	Dialog.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_DIM_BEHIND);
	//简单吧，就一句话。如果你使用的是API 版本是 14+，还可以更简单。
	Dialog.getWindow().setDimAmount(0);
	```