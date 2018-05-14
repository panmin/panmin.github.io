--
layout:     post                            # 使用的布局（不需要改）
title:      Android proguard代码混淆             # 标题
subtitle:      #副标题
date:       2015-11-08                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


1. 写在视图xml中的onClick响应出错. 因为写在xml中的onClick是通过反射调用的, proguard认为它们没有在代码中被调用过, 所以将它们从代码中除掉或改名了. 解决方法:
在proguard.cfg中添加以下代码, 就可以防止被配在视图xml中的onClick方法被proguard优化掉
-keepclassmembers class * extends android.app.Activity {
public void *(android.view.View);
}

a. 通配符 与java类似  *-》代表匹配所有字符   **表示该包类所有类及其子路径的类

b. 如果保持一个类不混淆-keep  class 类名 {*;}  类名是全名（包含包名）   

c. 保持一个类的子类不被混淆   -keep * extends 类名 { *;} 跟java很像是不是？

e. 保持一个类的单个方法不变 -keepclassmembers class 类名 {可含有通配符的方法名}

f. 保持含有某个方法的类不被混淆 -keepclasseswithmembers class 类名 {可含有通配符的方法名}

一、不能混淆的有：
1、四大组件不能混淆：由于四大组件是在清单文件里配置的，混淆后就不能依据清单文件的配置找到对应文件了
2、support包不能混淆
3、资源文件、R文件
4、js调用
5、反射类和调用反射的类
6、jni调用
7、so库、第三方jar包