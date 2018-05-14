--
layout:     post                            # 使用的布局（不需要改）
title:      Android Launcher开发             # 标题
subtitle:      #副标题
date:       2016-12-30                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


要点：
1. 在AndroidManifest.xml中添加以下粗体部分代码
```
 <application
     android:allowBackup="true"
     android:icon="@mipmap/ic_launcher"
     android:label="@string/app_name"
     android:supportsRtl="true"
     android:theme="@style/AppTheme">
     <activity android:name=".MainActivity">
         <intent-filter>
                  <action android:name="android.intent.action.MAIN"/>

                  <category android:name="android.intent.category.LAUNCHER"/>

                  <category android:name="android.intent.category.HOME"/>
                  <category android:name="android.intent.category.DEFAULT"/>
         </intent-filter>
     </activity>
  </application>
```

2. 在模拟器上按home键就能出现launcher的选择，真机上不行