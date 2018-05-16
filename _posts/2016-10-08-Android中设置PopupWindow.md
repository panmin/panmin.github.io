---
layout:     post                            # 使用的布局（不需要改）
title:      Android中设置PopupWindow                  # 标题
subtitle:           #副标题
date:       2016-10-08                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中设置PopupWindow

1. 设置在界面上显示，必须在初始化时设置高度和宽度
	```
	mPopupWindow = new PopupWindow(popupWindow, popupWindowWidth, LinearLayout.LayoutParams.WRAP_CONTENT);
	//设置可以获取焦点，否则弹出菜单中的EditText是无法获取输入的
	mPopupWindow.setFocusable(true);
	
	//这句是为了防止弹出菜单获取焦点之后，点击activity的其他组件没有响应
	mPopupWindow.setBackgroundDrawable(new BitmapDrawable());
	
	//防止虚拟软键盘被弹出菜单遮住
	mPopupWindow.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);
	mPopupWindow.setOnDismissListener(new PopupWindow.OnDismissListener() {
	  @Override
	  public void onDismiss() {
	  	cbremember.setChecked(true);
	  }
	});
	if (data.size() > 3) {
	  	mPopupWindow.setHeight((int)getResources().getDimension(R.dimen.dimen_48dp)*3);
	}
	```

	如果设置了PopupWindow可获取焦点的话，此时会遇到一个问题就是当PopupWindow中的控件比如EditText获取焦点之后，点击PopupWindow之外的控件是不会有响应的，如果用setBackgroundDrawable(new BitmapDrawable())进行设置的话，则不会出现这种情况.

	使用showAsDropDown时，如果没有充足的空间显示PopupWindow，那么PopupWindow的左下角将位于anchor的左上角来显示，如果想一直显示在下方可以用showAtLocation，例如：

	```
	int[] location = new int[2];
	etusername.getLocationOnScreen(location);
	mPopupWindow.showAtLocation(etusername,NO_GRAVITY,(int) getResources().getDimension(R.dimen.activity_horizontal_margin),location[1]+etusername.getHeight());
	```