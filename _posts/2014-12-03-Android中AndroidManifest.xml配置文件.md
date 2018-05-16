---
layout:     post                            # 使用的布局（不需要改）
title:      Android中AndroidManifest.xml配置文件                  # 标题
subtitle:           #副标题
date:       2014-12-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中AndroidManifest.xml配置文件

1. 作用：记录应用的相关配置信息，如包名、版本号、组件、权限等信息
2. 全局信息配置
    2.1. 应用的包名以及版本信息
	```
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
	    package="com.example.panmin"  包名
	    android:versionCode= "1"   软件版本信息
	    android:versionName= "1.0" >  
    ``` 
	2.2. android的版本信息：可支持的最低配置和期望的系统版本
	```
    <uses-sdk
        android:minSdkVersion ="8"
        android:targetSdkVersion ="17" />
	```
3. 在配置文件中注册组件（四大组件）
    3.1. 组件都写在application中（每个android中只能有一个application节点），application属性：
	```
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"   图标
        android:label="@string/app_name"   标题
        android:theme="@style/AppTheme" >  主题样式
	```
    3.2. activity组件：每个需要显示的activity都需要在application节点下配置，没有配置的话打开会报异常
	```
    <activity
         android:name="com.example.panmin.MainActivity"   最好能用全名的方式
         android:label="@string/app_name">  标题
            <intent-filter>  可以用来组件之间的通信，控制哪个个组件调用哪个组件，代码中用Intent
                <action android:name="android.intent.action.MAIN" />  用来决定应用程序最先启动的activity
                <category android:name="android.intent.category.LAUNCHER" /> 用来决定应用程序是否显示在程序列表中，这两句同时存在时该activity才会显示在程序列表中  一个程序里有2个Activity， 2个Activity都加入了main和launcher ，结果会在程序列表里有2个程序图标分别运行，单击eclipse中的run时，在AndroidManifest.xml文件里，那个靠前那个会先启动。
				只有最前面的Activity的 <action android:name="android.intent.action.MAIN"/> 有效，启动该程序时，执行的是该Activity，而因为两个activity同时设定，所以会出现两个图标
            </intent-filter>
    </activity>
	```