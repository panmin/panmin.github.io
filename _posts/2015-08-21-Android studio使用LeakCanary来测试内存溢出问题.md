---
layout:     post                            # 使用的布局（不需要改）
title:      Android studio使用LeakCanary来测试内存溢出问题                   # 标题
subtitle:           #副标题
date:       2015-08-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


### 使用方法
因为不想让这样的检查在正式给用户的 `release` 版本中也进行，所以在 `dependencies` 里添加
```
dependencies {    
      compile fileTree(dir: 'libs', include: ['*.jar'])    
      compile 'com.android.support:support-v13:+'    
      debugCompile 'com.squareup.leakcanary:leakcanary-android:1.3'    
      releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3'
}
```

接下来，在你的应用里写一个自定义 `Application` ，并在其中“安装” `RefWatcher` ：
```
public class AppApplication extends Application {    
      private RefWatcher mRefWatcher;    
      @Override    
      public void onCreate() {        
           super.onCreate();        
           mRefWatcher = LeakCanary.install(this);    
      }
}
```


记得把它作为 `android:name` 配到 `AndroidManifest.xml` 的 `Application` 节点下。