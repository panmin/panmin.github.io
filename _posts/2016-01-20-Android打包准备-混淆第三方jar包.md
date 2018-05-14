--
layout:     post                            # 使用的布局（不需要改）
title:      Android打包准备-混淆第三方jar包             # 标题
subtitle:   Gson, greenDao,sharesdk,UIL,高德地图, unity,pinyin4j,async http,JPush,EventBus等混淆   #副标题
date:       2016-01-20                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


1、Gson混淆
```
## ----------------------------------
##   ########## Gson混淆    ##########
## ----------------------------------
-keepattributes Signature
-keep class sun.misc.Unsafe { *; }
-keep class com.google.gson.examples.android.model.** { *; }
```

2、greenDao混淆
```
# # -------------------------------------------
# #  ######## greenDao混淆  ########## 
# # -------------------------------------------
-libraryjars libs/greendao-1.3.7.jar
-keep class de.greenrobot.dao.** {*;}
-keepclassmembers class * extends de.greenrobot.dao.AbstractDao {
    public static java.lang.String TABLENAME;
}
-keep class **$Properties
```
 
3、volley混淆
``` 
# # -------------------------------------------
# #  ############### volley混淆  ############### 
# # -------------------------------------------
-keep class com.android.volley.** {*;}
-keep class com.android.volley.toolbox.** {*;}
-keep class com.android.volley.Response$* { *; }
-keep class com.android.volley.Request$* { *; }
-keep class com.android.volley.RequestQueue$* { *; }
-keep class com.android.volley.toolbox.HurlStack$* { *; }
-keep class com.android.volley.toolbox.ImageLoader$* { *; }
```
 
4、sharesdk混淆
```
## ----------------------------------
##      sharesdk
## ----------------------------------
-keep class cn.sharesdk.**{*;}
-keep class com.sina.**{*;}
-keep class **.R$* {*;}
-keep class **.R{*;}
-dontwarn cn.sharesdk.**
-dontwarn **.R$*
```
 
5、UIL混淆
```
## ----------------------------------
##      UIL相关
## ----------------------------------
-keep class com.nostra13.universalimageloader.** { *; }
-keepclassmembers class com.nostra13.universalimageloader.** {*;}
```

6、高德地图sdk混淆
```
## ----------------------------------
##      高德地图sdk混淆
## ----------------------------------
-libraryjars   libs/android-support-v4.jar
-dontwarn android.support.v4.**   
-keep class android.support.v4.** { *; } 
-keep interface android.support.v4.app.** { *; } 
-keep public class * extends android.support.v4.** 
-keep public class * extends android.app.Fragment
-libraryjars   libs/MapApiLocation.jar
-libraryjars   libs/MapApiSearch.jar
-libraryjars   libs/Android_Map_V2.0.4.jar
-dontwarn com.amap.api.**  
-dontwarn com.a.a.**  
-dontwarn com.autonavi.**  
-keep class com.amap.api.**  {*;}     
-keep class com.autonavi.**  {*;}
-keep class com.a.a.**  {*;}
```

7、unity混淆(需要修改proguard.jar文件)
```
## ---------------------------------------------------------------
##      unity混淆(需要修改proguard.jar文件)
## ---------------------------------------------------------------
-libraryjars f:/work_hb_v021/Lib_SocShare_v237_0804/libs/untiy-classes.jar
-dontwarn com.unity3d.player.**   
-dontwarn org.fmod.**  
-keep class com.unity3d.player.**{*;} 
-keep class org.fmod.**{*;} 
-keep public class * extends com.unity3d.player.**
-keep public class * extends org.fmod.**
```
注：还需要对与unity交互的相关方法或者类(jni)进行混淆配置。

8、内部类或者内部接口类的混淆配置
```
# # -------------------------------------------
# #  ######## 内部类混淆配置  ########## 
# # -------------------------------------------
-keep class com.manjay.housebox.activity.CityListActivity$*{
        <fields>;
        <methods>;
}
-keepclassmembers class com.manjay.housebox.activity.CityListActivity$*{*;}
 
-keep class com.manjay.housebox.map.MapActivity$*{
        <fields>;
        <methods>;
}
-keepclassmembers class com.manjay.housebox.map.MapActivity$*{*;}
```
 
9、pinyin4j
```
-dontwarn net.soureceforge.pinyin4j.**
-dontwarn demo.**
-libraryjars libs/pinyin4j-2.5.0.jar
-keep class net.sourceforge.pinyin4j.** { *;}
-keep class demo.** { *;}
```
 
10、volley
```
-libraryjars libs/volley.jar
```
 
11、async http
```
-libraryjars libs/android-async-http-1.4.5.jar
```
 
12、JPush
```
-libraryjars libs/jpush-sdk-release1.6.3.jar
-dontwarn cn.jpush.**
-keep class cn.jpush.** { *; }
```
 
13、EventBus
```
-libraryjars libs/eventbus-1.0.1.jar
-keepclassmembers class ** {
    public void onEvent*(**);
}
-keepclassmembers class ** {
	public void xxxxxx(**); //所有监听的方法都要列在这里
}
```
 
14、SlidingMenu
```
-dontwarn com.jeremyfeinstein.slidingmenu.lib.**
-keep class com.jeremyfeinstein.slidingmenu.lib.**{*;}
```
 
15、ActionBarSherlock
```
-dontwarn com.actionbarsherlock.**
-keep class com.actionbarsherlock.**{*;}
```
 
17、asmack-android-19-0.8.10.jar 聊天xmpp 第三方jar
```
-libraryjars libs/asmack-android-19-0.8.10.jar
-keep class com.kenai.jbosh.** {*; }
-keep class com.novell.sasl.client.** {*; }
-keep class de.measite.smack.** {*; }
-keep class org.** {*; }
```
```
/**如果项目用到jar的接口  此方不加，会有问题**/
-keep interface com.kenai.jbosh.** {*; }
-keep interface com.novell.sasl.client.** {*; }
-keep interface de.measite.smack.** {*; }
-keep interface org.** {*; }
```