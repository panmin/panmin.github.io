---
layout:     post                            # 使用的布局（不需要改）
title:      eclipse项目导入android studio                   # 标题
subtitle:           #副标题
date:       2015-08-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


如果你之前有用Eclipse做过安卓开发，现在想要把Eclipse中的项目导入到Android Studio的环境中，那么首先要做的是生成Build Gradle的文件。因为Android Studio 是用Gradle来管理项目的，具体操作步骤如下：

 

从Eclipse中导出
        1. 将你的ADT插件版本升级到22.0以上。
        2. 在Eclipse中，选择File-->Export。
        3. 在弹出的导出窗口中，打开Android的文件夹，选择“Generate Gradle Build Files”。
        4. 选中你想要导入到Android Studio中的项目，Finish。
PS:导出的项目将会和原来的项目在同一目录，覆盖原来的同时，会新增一个叫build.gradle的文件，导入Android Studio时将首先读取这个文件。

导入到Android Studio
        1. 在Android Studio 中，首先关掉你当前的打开的项目。
        2. 在欢迎界面，点击Import Project（注：也是可以直接在菜单选择Import project的）
        3. 选中你在Eclipse中导出的项目，展开目录，点击build.gradle文件，然后OK（千万记得点击这个build.gradle 文件，而不是选择文件夹）
        4. 在之后的弹出对话框中，会要求你选择Gradle的配置，选中Use gradle wrapper.(注：也可以自定义你本机装的Gradle)
        
        
PS：如果没有Grade build文件，也是可以将普通的安卓项目导入到Android Studio中，它会用现有的Ant build.但为了更好地使用之后的功能和充分使用构建变量，还是强烈地建议先从ADT插件中生成Gradle文件再导入Android Studio