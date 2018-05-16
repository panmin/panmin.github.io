---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Event Bus模式                   # 标题
subtitle:           #副标题
date:       2015-09-05                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


### 场景描述：
对于事件总线框架知道的人可能不多，但是进行过Android开发的程序猿们应该都有这样的经历。
1、在开发的过程中遇到过从Activity-A跳转到Activity-B，然后需要在Activity-B处理完某些工作之后回调Activity-A中的某个函数，但Activity又不能手动创建对象来设置一个Listener的情况。
2、遇到在某个Service中更新Activity或Fragment中的界面等组件之间的交互问题。
3、彻底退出应用的功能实现，需要管理activity列表的烦恼。
4、后台下载需要通知各个组件的情况。
5、Fragment之间的通信问题。


### 有什么用：
事件总线框架简化了Activity、Fragment、Service等组件之间的交互，让代码更简洁，耦合性更低，相比以往的广播或者序列化对象来传递，事件总线就简约和高效了很多。Event Bus模式也被称为Message Bus或者发布者/订阅者(publisher/subscriber)模式，可以让两个组件相互通信，但是他们之间并不相互知晓。



#### 主要作用
1、它承担传输数据的作用
2、它可以解耦模块之间的耦合性
3、简化代码逻辑
4、相比传统方法要更加高效
5、消息可以在任意线程和位置发送
6、接受消息并执行逻辑的方法可以在任意线程运行（可以设置运行的线程）
7、消息与接收者可以是一对多的关系（类似广播）



### 基本的用法：
市面上这么多的事件总线框架他们的用法都是大同小异的，基本包括下面几个逻辑和使用步骤
1、注册信息订阅者/接收者（可以理解为需要被调用的那一方）
2、定义事件（类似handle中的Message）
3、实现订阅者的方法（消息发送后被调用的方法）
4、发送消息


### EventBus框架
> EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。

**特点：**
1. 在EventBus中，使用约定来指定事件订阅者以简化使用。即所有事件订阅都都是以onEvent开头的函数，具体来说，函数的名字是onEvent，onEventMainThread，onEventBackgroundThread，onEventAsync这四个。简单理解就是需要被调用的方法名称需要按照上面的规则来写，不能自定义。
2. 效率高

Github地址和文档：
https://github.com/greenrobot/EventBus


### Otto框架
> Otto 是Android系统的一个Event Bus模式类库。用来简化应用组件间的通信。

**特点：**
1. 使用注解的方式，@Subscribe 注解告诉Bus该函数订阅了一个事件，该事件的类型为该函数的参数类型；而@Produce注解告诉Bus该函数是一个事件产生者，产生的事件类型为该函数的返回值。函数名称可以自定义。
2. 订阅函数默认执行在主线程中

官方网站：
http://square.github.io/otto/
Github地址：
https://github.com/square/otto