--
layout:     post                            # 使用的布局（不需要改）
title:      Android MVP             # 标题
subtitle:      #副标题
date:       2016-06-01                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---




对于一个应用而言我们需要对它抽象出各个层面，而在MVP架构中它将UI界面和数据进行隔离，所以我们的应用也就分为三个层次。
* **View**: 对于View层也是视图层，在View层中只负责对数据的展示，提供友好的界面与用户进行交互。在Android开发中通常将Activity或者Fragment作为View层。
* **Model**: 对于Model层也是数据层。它区别于MVC架构中的Model，在这里不仅仅只是数据模型。在MVP架构中Model它负责对数据的存取操作，例如对数据库的读写，网络的数据的请求等。
* **Presenter**：对于Presenter层他是连接View层与Model层的桥梁并对业务逻辑进行处理。在MVP架构中Model与View无法直接进行交互。所以在Presenter层它会从Model层获得所需要的数据，进行一些适当的处理后交由View层进行显示。这样通过Presenter将View与Model进行隔离，使得View和Model之间不存在耦合，同时也将业务逻辑从View中抽离。


在MVP架构中将这三层分别抽象到各自的接口当中。通过接口将层次之间进行隔离，而Presenter对View和Model的相互依赖也是依赖于各自的接口。这点符合了接口隔离原则，也正是面向接口编程。在Presenter层中包含了一个View接口，并且依赖于Model接口，从而将Model层与View层联系在一起。而对于View层会持有一个Presenter成员变量并且只保留对Presenter接口的调用，具体业务逻辑全部交由Presenter接口实现类中处理。


#### 官方MVP架构分析
对于MVP架构有了一些的了解，而在前端时间Google给出了一些App开发架构的实现。 
项目地址为：https://github.com/googlesamples/android-architecture. 
在这里进入README看一下这次Google给出那些Android开发架构的实现。