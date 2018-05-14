--
layout:     post                            # 使用的布局（不需要改）
title:      Android中scrollView套ListView，ListView高度不适配，滚动条位置不对             # 标题
subtitle:      #副标题
date:       2016-11-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


1. 重写ListView实现onMeasure重新计算每行的高度

	```
	public class MyListView extends ListView {
	
	    public MyListView(Context context) {
	        super(context);
	    }
	
	    public MyListView(Context context, AttributeSet attrs) {
	        super(context, attrs);
	    }
	
	    public MyListView(Context context, AttributeSet attrs,
	                      int defStyle) {
	        super(context, attrs, defStyle);
	    }
	
	    @Override
	    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
	        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2,
	                MeasureSpec.AT_MOST);
	        super.onMeasure(widthMeasureSpec, expandSpec);
	    }
	
	}
	```

2. 控制scrollView滚动条的位置，防止顶部部分会被遮盖

	```
	mScrollView.smoothScrollTo(0,20);
	```

	无效时， 在代码里去掉listview的焦点 **lv.setFocusable(false);**