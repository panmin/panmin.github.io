---
layout:     post                            # 使用的布局（不需要改）
title:      Android FileProvider配置报错android.content.pm.ProviderInfo.loadXmlMetaData问题              # 标题
subtitle:   FileProvider配置访问路径，适配7.0及其以上，配置包名不对，导致报错   #副标题
date:       2018-01-28                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


### 前言

> 适配7.0及其以上，需要配置FileProvider，否则使用**Intent**携带`file://`类型的**URI**去打开外部App**(比如：打开系统相机拍照)**，那么会抛出**FileUriExposedException**

```
java.lang.NullPointerException: Attempt to invoke virtual method 
       'android.content.res.XmlResourceParser android.content.pm.ProviderInfo.loadXmlMetaData(android.content.pm.PackageManager, java.lang.String)' on a null object reference
        at android.support.v4.content.FileProvider.parsePathStrategy(FileProvider.java:583)
        at android.support.v4.content.FileProvider.getPathStrategy(FileProvider.java:557)
        at android.support.v4.content.FileProvider.getUriForFile(FileProvider.java:399)
```


### 查找配置

先查看AndroidManifest.xml中FileProvider配置：

```
<!-- FileProvider配置访问路径，适配7.0及其以上 -->
<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="${applicationId}.provider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths"/>
</provider>
```

在查看调用时候，在 FileProvider.getUriForFile()中传入的authorities:

```
FileProvider.getUriForFile(context.getApplicationContext(), BuildConfig.APPLICATION_ID+".provider",new File(picturePath))
```
这就奇怪了，感觉没错呀。 这时候，很大可能就是导入BuildConfig错误了，查看导入包。

```
import android.support.v4.BuildConfig;//自动导包错误
```
果然如此，错误导入v4包下的BuildConfig。这导致配置的authority与传入的authority不一样，当然会报错的啦。

**正确的做法：**
导入自己项目的包下的BuildConfig。
```
//导入自己项目的包
import com.panmin.demo.BuildConfig;

FileProvider.getUriForFile(context.getApplicationContext(), BuildConfig.APPLICATION_ID+".provider",new File(picturePath))
```