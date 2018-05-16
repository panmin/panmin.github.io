---
layout:     post                            # 使用的布局（不需要改）
title:      Android Timer的使用                  # 标题
subtitle:   不能在Timer中直接更新UI控件        #副标题
date:       2015-06-09                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android Timer的使用(不能在Timer中直接更新UI控件)

1. 初始化Timer和TimerTask
	```
	private Timer timer = null;
    private TimerTask task =new TimerTask() {
       @Override
       public void run() {
          /**
          * 更新UI控件的第一种方法：
          * 使用handler
          */
          //handler.sendEmptyMessage(0);
          /**
          * 直接在这里面更新界面会直接崩溃
          */
          //tv.setText("aaaaaaaaaaa");
         
          /**
          * 更新UI控件的第二种方法：
          * 在activity中可以使用runOnUiThread来更新UI
          */
         runOnUiThread( new Runnable() {
               
                @Override
                public void run() {
                       tv.setText( "aaaaaaaaaaa");
                      
               }
         });
      }
   	};     
	```

	```
	timer = new Timer(true);
    timer.schedule( task, 1000, 1000); //延时1000ms后执行，1000ms执行一次
	```
	取消Timer
	```
    timer.cancle();
	```

	如果想再次启动timer必须重新初始化Timer和TimerTask，TimerTask初始化一次是不行的

注：不能在Timer中直接更新UI控件
handler可以使用handler.postDelay(runable,延迟执行时间)来模拟Timer，
区别：Timer是异步线程，handler是运行在主线程（在activity中的指UI线程）中的。