--
layout:     post                            # 使用的布局（不需要改）
title:      Android design support library--侧边导航栏             # 标题
subtitle:      #副标题
date:       2016-05-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---



1. 添加包`compile 'com.android.support:design:23.4.0'`
2. activity的layout文件，根节点使用**android.support.v4.widget.DrawerLayout** ,添加属性
	```
	xmlns:tools="http://schemas.android.com/tools"  
	tools:openDrawer="start"
	```
3. DrawerLayout控件中添加**android.support.design.widget.NavigationView**作为侧边栏，正文内容需要添加**ToolBar**自定义标题栏（**需要style中设置成NoTitleBar的主题**），如：
	```
	 <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
        <android.support.v7.widget.Toolbar
            android:id="@+id/toolBar"
            android:layout_width="match_parent"
            android:layout_height="50dp"/>
     </LinearLayout>
	```
4. 设置**ActionBar**
	```
	android.support.v7.widget.Toolbar toolBar = (Toolbar) findViewById(R.id.toolBar);
    setSupportActionBar(toolBar);
    ActionBar actionBar = getSupportActionBar();
    if(actionBar!=null){
        actionBar.setHomeAsUpIndicator(R.mipmap.ic_launcher);
        actionBar.setDisplayHomeAsUpEnabled(true);
    }
	```
5. 关键来了**android.support.design.widget.NavigationView**设置
	```
	<android.support.design.widget.NavigationView
        android:id="@+id/navView"
        android:layout_width="200dp"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:menu="@menu/left_menu"
        app:headerLayout="@layout/left_layout"/>
	```
	left_menu.xml在menu文件夹中，控制侧边栏中的列表界面
	left_layout.xml在layout文件夹中，控制侧边栏头部界面

	**NavigationView的点击事件**
	````
	navView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
        @Override
        public boolean onNavigationItemSelected(MenuItem item) {
            switch (item.getItemId()){

            }
            return false;
        }
    });
	```