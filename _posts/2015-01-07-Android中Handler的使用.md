---
layout:     post                            # 使用的布局（不需要改）
title:      Android中Handler的使用                  # 标题
subtitle:           #副标题
date:       2015-01-07                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中Handler的使用


#### handler是什么？
> 主要接收子线程发送的数据，并用此数据来配合主线程更新UI

 解释：当应用程序启动时， Android首先会开启一个主线程（也就是 UI线程），主线程为管理界面中的 UI控件，进行事件的分发，比如说，你要点击一个 Button,Android会分发事件到Button上，来响应你的操作。如果此时需要一个耗时的操作，例如：联网读取数据，或者读取本地较大的一个文件的时候，你不能把这些操作放到主线程中，如果你放到主线程中的话，界面会出现假死的现象，**如果 5秒钟还没有完成的话，会收到 Android系统的一个错误提示“强制关闭”**，这个时候我们需要把这些耗时的操作，放到一个子线程中，因为子线程涉及大 UI更新，android 主线程是线程不安全的，也就是说，更新 UI只能在主线程中更新，子线程中操作是危险的，这个时候， Hannler就出现了，来解决这个复杂的问题，由于 Handler运行在主线程中（UI线程中），它与子线程可以通过 Message对象来传递数据，这个时候， Handler就承担着接收子线程传过来的（子线程用 SendMessage()方法来传递）Message对象，里面包含数据，把这些消息放到主线程队列中，配合主线程进行更新 UI.


#### 为什么要用handler
handler可以分发Message对象和Runnable对象到主线程中，每个Handler实例，都会绑定到创建他的线程中（一般是位于主线程）；
它有两个作用：（1）安排消息或者Runnable在某个主线程中某个地方执行（2）安排一个动作在不同的线程中执行。


#### handler的原理
handler封装了消息的发送

Looper内部包含一个消息队列也就是MessageQueue，所有的handler发送的消息都是走的这个消息队     列；Looper.Looper方法是一个死循环，不断的从MessageQueue取消息，如果有消息就处理消息，没有消息就阻塞。

MessageQueue是一个消息队列，可以添加消息并处理消息。

Handler在构造方法中会与Looper进行关联，通过MessageQueue来发送消息。

Handler负责发送消息，Looper负责接收Handler发送的消息，并把消息回传个handler自己，MessageQueue就是消息的一个存储容器。