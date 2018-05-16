---
layout:     post                            # 使用的布局（不需要改）
title:      Java中lock和Synchronized的使用                  # 标题
subtitle:           #副标题
date:       2015-06-05                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---


## Java中lock和Synchronized的使用
```
Lock lock = new ReentrantLock();
lock .lock();//加锁
lock .unlock();//释放锁
```
线程A和B都要获取O对象，假设A获取了对象O锁，B将等待A释放O的锁定
如果使用synchronized,如果A不释放，B将一直等下去，不能被中断
如果使用ReentrantLock，如果A不释放，可以使B在等待了足够长的时间后，中断等待，而干别的事情