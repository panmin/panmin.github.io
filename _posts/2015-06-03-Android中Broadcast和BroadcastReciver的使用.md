---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Broadcast和BroadcastReciver的使用                  # 标题
subtitle:           #副标题
date:       2015-06-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

## Android中Broadcast和BroadcastReciver的使用

1. Broadcast和BroadcastReciver是订阅者模式，主要作用是能够实现Service和Activity之间的通讯，或者是不同的进程、不同的app之间通讯。
2. Broadcast分类:
     2.1. 有序广播：发送方发送之后，几乎同时到达多个广播接收者处，并且无法终止广播的继续传播，使用Context.sendBroadcast(intent)
     2.2. 有序广播：给广播接收者设置优先级，优先级越高的越先接收到广播，有序广播能终止广播(abortBroadcast();)；使用Context.sendOrderedBroadcast(intent)
3. 发送广播时设置Intent
	```
	Intent intent=new Intent();
    intent.setAction("action");//必须设置Action
	```
4. 接收广播
	4.1. 初始化广播接收者
	```
	BroadcastReceiver receiver=new BroadcastReceiver(){
           @Override
           public void onReceive(Context context, Intent intent) {
              if(intent.getAction().equals("action" )){//必须广播过滤，防止其他广播的干扰
                    //TODO
             }
          }
       }; 
	```
	4.2. 注册广播接收者
	```
	IntentFilter filter = new IntentFilter( "action");//必须设置过滤器，和在onReceive中过滤一样，这里也是设置发送广播时的Action
    Context.registerReceiver( receiver, filter);
	```
	4.3. 注销广播接收者
	```
	Context.unregisterReceiver(receiver);  
	```

	注：当重写BroadcastReciver时，可以在mainfest.xml文件中进行注册广播接收者。如：
	```
	public class MyReceiver extends BroadcastReceiver {  
	      
	    private static final String TAG = "MyReceiver";  
	      
	    @Override  
	    public void onReceive(Context context, Intent intent) {  
	        String msg = intent.getStringExtra("msg");  
	        Log.i(TAG, msg);  
	    }  
	} 
	```
	```
	<receiver android:name=".MyReceiver">  
	    <intent-filter>  
	        <action android:name="android.intent.action.MY_BROADCAST"/>  
	        <category android:name="android.intent.category.DEFAULT" />  
	    </intent-filter>  
	</receiver>  
	```
5. BroadcastReciver接收系统广播，如开机启动，网络状态改变、拍照、屏幕关闭与开启、电量不足等。