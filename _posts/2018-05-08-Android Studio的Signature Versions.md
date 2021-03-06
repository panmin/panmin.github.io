---
layout:     post                            # 使用的布局（不需要改）
title:      Android Studio的Signature Versions      # 标题
subtitle:   打包签名版本时V1打包jar，V2打包apk         #副标题
date:       2018-05-08                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

## 

![]({site}/../img/signature_versions.png)

打包一个文件的签名版本，

选V1打包出来的app是jar的（一般这种就是当做第三方导入项目来用的），

选v2打包出来的APP是apk版本的（也就是可以直接在手机上安装的，可是上线的）；


#### APK signature scheme v2
Android 7.0 引入一项新的应用签名方案 APK Signature Scheme v2，它能提供更快的应用安装时间和更多针对未授权 APK 文件更改的保护。在默认情况下，Android Studio 2.2 和 Android Plugin for Gradle 2.2 会使用 APK Signature Scheme v2 和传统签名方案来签署您的应用。

虽然我们建议您对您的应用采用 APK Signature Scheme v2，但这项新方案并非强制性的。如果您的应用在使用 APK Signature Scheme v2 时不能正确开发，您可以停用这项新方案。禁用过程会导致 Android Studio 2.2 和 Android Plugin for Gradle 2.2 仅使用传统签名方案来签署您的应用。要仅用传统方案签署，打开模块级 build.gradle 文件，然后将行 v2SigningEnabled false 添加到您的版本签名配置中：
```
  android {
    ...
    defaultConfig { ... }
    signingConfigs {
      release {
        storeFile file("myreleasekey.keystore")
        storePassword "password"
        keyAlias "MyReleaseKey"
        keyPassword "password"
        v2SigningEnabled false
      }
    }
  }
```
注意：如果您使用 APK Signature Scheme v2 签署您的应用，并对应用进行了进一步更改，则应用的签名将无效。出于这个原因，请在使用 APK Signature Scheme v2 签署您的应用之前、而非之后使用 `zipalign` 等工具。

如需了解详细信息，请阅读相关的 Android Studio 文档，这些文档介绍了如何在 Android Studio 中[签署应用](https://developer.android.google.cn/studio/publish/app-signing.html#release-mode)以及如何使用 Android Plugin for Gradle [为签署应用配置构建文件](https://developer.android.google.cn/studio/build/build-variants.html#signing)。