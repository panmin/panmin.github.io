---
layout:     post                            # 使用的布局（不需要改）
title:      Android中继承ViewGroup类控件，实现onMeasure和onLayout                  # 标题
subtitle:           #副标题
date:       2014-12-23                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中继承ViewGroup类控件，实现onMeasure和onLayout

1. onMeasure 用来设置子View的宽和高，设置自己的宽和高
	```
	protected void onMeasure (int widthMeasureSpec, int heightMeasureSpec) {
           // TODO Auto-generated method stub
           super.onMeasure(widthMeasureSpec, heightMeasureSpec);
           if (!once ) {
        //通过getChildAt来获取子View
             LinearLayout vg = (LinearLayout) this.getChildAt(0);
              mMenu = (ViewGroup) vg.getChildAt(0);
              mContent = (ViewGroup) vg.getChildAt(1);
              /**
              * mMenu.getLayoutParams().width 用来给子View设置宽度
              * mScreenWidth 通过构造方法获得
              * WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
                DisplayMetrics outMetrics = new DisplayMetrics();
                wm.getDefaultDisplay().getMetrics(outMetrics);
                mScreenWidth = outMetrics.widthPixels;
              */
              mMenuWidth = mMenu .getLayoutParams().width = mScreenWidth;
             
              mContent.getLayoutParams(). width= mScreenWidth;
              once = true;
          }
    }
	```
2. onLayout 用来设置View的位置
	```
	protected void onLayout(boolean changed, int l, int t, int r, int b) {
       // TODO Auto-generated method stub
       super.onLayout(changed, l, t, r, b);
       if (changed) {
          this.scrollTo(mMenuWidth , 0);
      }
    }
	```