---
layout:     post                            # 使用的布局（不需要改）
title:      Android SeekBar自定义使用图片和颜色显示                   # 标题
subtitle:            #副标题
date:       2015-10-27                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android SeekBar自定义使用图片和颜色显示

1. 在res/drawable目录下新增一个xml风格文件，seekbar_define_style.xml
	```
	<?xml version="1.0" encoding="utf-8"?>
	<layer-list
	    xmlns:android="http://schemas.android.com/apk/res/android">
	    <!-- 未选中 -->
	    <item
	        android:id="@android:id/background"
	        android:drawable="@drawable/hou"/>
	    <!-- 中 -->
	    <item
	        android:id="@android:id/progress"
	        android:drawable="@drawable/qian"/>
	    <item
	        android:id="@android:id/secondaryProgress"
	        android:drawable="@drawable/qian"/>
	</layer-list>
	```

2. 在res/drawable下定义个seekbar_thumb.xml文件
	```
	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android">       
	    <!-- 按下状态--> 
	    <item   
	        android:state_focused="true"   
	        android:state_pressed="true"   
	        android:drawable="@drawable/ic_launcher" />       
	    <!-- 普通无焦点状态 -拖动按钮--> 
	    <item   
	        android:state_focused="false"   
	        android:state_pressed="false" 
	        android:drawable="@drawable/orbino_icon_pack_006" />             
	    <!-- 有焦点状态--> 
	    <item   
	        android:state_focused="true"   
	        android:state_pressed="false"             
	        android:drawable="@drawable/ios" />        
	    <!-- 有焦点 --> 
	    <item   
	        android:state_focused="true"             
	        android:drawable="@drawable/ios"/>
	</selector>
	```

3. 在res/layout下定义布局资源文件seekbar_define.xml
	```
	<?xml version="1.0" encoding="utf-8"?>
	<ScrollView xmlns:android="<a href="http://schemas.android.com/apk/res/android" rel="nofollow">http://schemas.android.com/apk/res/android</a>"
	    android:layout_width="fill_parent"
	    android:layout_height="fill_parent"
	    >
	    <LinearLayout
	        android:layout_width="fill_parent"
	        android:layout_height="fill_parent"
	         android:orientation="vertical"
	        >
		    <TextView
			    android:id="@+id/seekbar_tetview_one"
			    android:layout_width="wrap_content"
			    android:layout_height="wrap_content"
			    android:text="SeekBar自定义"
			    />
		    <TextView
			    android:id="@+id/seekbar_tetview_two"
			    android:layout_width="wrap_content"
			    android:layout_height="wrap_content"
			    android:text="SeekBar拖动时信息提示"
			    />
		    <SeekBar
			    android:layout_width="321px"
			    android:layout_height="wrap_content"
			    android:layout_centerInParent="true"
			    android:maxHeight="20px"
			    android:minHeight="20px"
			    android:paddingLeft="18px"
			    android:paddingRight="18px"
			    android:max="100"
			    android:progressDrawable="@drawable/seekbar_define_style"
			    android:thumb="@drawable/seekbar_thumb"
			    android:id="@+id/seekBar"/>
	 	</LinearLayout>
	</ScrollView>
	```

4. 定义java文件通过 引用布局文件：
	```
	import android.R.integer;
	import android.app.Activity;
	import android.os.Bundle;
	import android.os.Handler;
	import android.os.Message;
	import android.widget.SeekBar;
	import android.widget.SeekBar.OnSeekBarChangeListener;
	import android.widget.TextView;
	 
	public class SeekBarDemo_DefineDemo extends Activity {
	    private SeekBar seekBar;
	    private TextView textView_one, textView_two;
	 
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.seekbar_define);
	 
	        seekBar = (SeekBar) findViewById(R.id.seekBar);
	 
	        textView_one = (TextView) findViewById(R.id.seekbar_tetview_one);
	 
	        textView_two = (TextView) findViewById(R.id.seekbar_tetview_two);
	 
	        seekBar.setOnSeekBarChangeListener(seekbarChangeListener);
	 
	    }
	 
	    private OnSeekBarChangeListener seekbarChangeListener = new OnSeekBarChangeListener() {
	 
	        // 停止拖动时执行
	        @Override
	        public void onStopTrackingTouch(SeekBar seekBar) {
	            // TODO Auto-generated method stub
	            textView_two.setText("停止拖动了！");
	 
	        }
	 
	        // 在进度开始改变时执行
	        @Override
	        public void onStartTrackingTouch(SeekBar seekBar) {
	            // TODO Auto-generated method stub
	            textView_two.setText("进度开始改变");
	        }
	 
	        // 当进度发生改变时执行
	        @Override
	        public void onProgressChanged(SeekBar seekBar, int progress,
	                boolean fromUser) {
	            textView_two.setText("正在进行拖动操作，还没有停下来一直再拖动");
	            Message message = new Message();
	 
	            Bundle bundle = new Bundle();// 存放数据
	 
	            float pro = seekBar.getProgress();
	 
	            float num = seekBar.getMax();
	 
	            float result = (pro / num) * 100;
	            bundle.putFloat("key", result);
	 
	            message.setData(bundle);
	 
	            message.what = 0;
	 
	            handler.sendMessage(message);
	 
	        }
	    };
	 
	    /**
	     * 用Handler来更新UI
	     */
	    private Handler handler = new Handler() {
	        @Override
	        public void handleMessage(Message msg) {
	            textView_one.setText("当前拖动位置占 : " + msg.getData().getFloat("key") + "/100");
	        }
	    };
	}
	```