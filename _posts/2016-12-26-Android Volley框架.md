---
layout:     post                            # 使用的布局（不需要改）
title:      Android Volley框架                  # 标题
subtitle:           #副标题
date:       2016-12-26                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android Volley框架

### 功能介绍
> Volley 是 Google 推出的 Android 异步网络请求框架和图片加载框架。在 Google I/O 2013 大会上发布。
> 特别适合数据量小，通信频繁的网络操作。


### Volley 的主要特点
1. 扩展性强。Volley 中大多是基于接口的设计，可配置性强。
2. 一定程度符合 Http 规范，包括返回 ResponseCode(2xx、3xx、4xx、5xx）的处理，请求头的处理，缓存机制的支持等。并支持重试及优先级定义。
3. 默认 Android2.3 及以上基于 HttpURLConnection，2.3 以下基于 HttpClient 实现。
4. 提供简便的图片加载工具。


### 总体设计
![](https://raw.githubusercontent.com/android-cn/android-open-project-analysis/master/tool-lib/network/volley/image/design.png)
上面是 Volley 的总体设计图，主要是通过两种`Dispatch Thread`不断从`RequestQueue`中取出请求，根据是否已缓存调用`Cache`或`Network`这两类数据获取接口之一，从内存缓存或是服务器取得请求的数据，然后交由`ResponseDelivery`去做结果分发及回调处理。


### Volley 中的概念
Volley 的调用比较简单，通过 `newRequestQueue(…)` 函数新建并启动一个请求队列 `RequestQueue` 后，只需要往这个 `RequestQueue` 不断 add Request 即可。

**Volley**：Volley 对外暴露的 API，通过 newRequestQueue(…) 函数新建并启动一个请求队列RequestQueue。
**Request**：表示一个请求的抽象类。StringRequest、JsonRequest、ImageRequest 都是它的子类，表示某种类型的请求。
**RequestQueue**：表示请求队列，里面包含一个CacheDispatcher(用于处理走缓存请求的调度线程)、NetworkDispatcher数组(用于处理走网络请求的调度线程)，一个ResponseDelivery(返回结果分发接口)，通过 start() 函数启动时会启动CacheDispatcher和NetworkDispatchers。
**CacheDispatcher**：一个线程，用于调度处理走缓存的请求。启动后会不断从缓存请求队列中取请求处理，队列为空则等待，请求处理结束则将结果传递给ResponseDelivery去执行后续处理。当结果未缓存过、缓存失效或缓存需要刷新的情况下，该请求都需要重新进入NetworkDispatcher去调度处理。
**NetworkDispatcher**：一个线程，用于调度处理走网络的请求。启动后会不断从网络请求队列中取请求处理，队列为空则等待，请求处理结束则将结果传递给ResponseDelivery去执行后续处理，并判断结果是否要进行缓存。
**ResponseDelivery**：返回结果分发接口，目前只有基于ExecutorDelivery的在入参 handler 对应线程内进行分发。
**HttpStack**：处理 Http 请求，返回请求结果。目前 Volley 中有基于 HttpURLConnection 的HurlStack和 基于 Apache HttpClient 的HttpClientStack。
**Network**：调用HttpStack处理请求，并将结果转换为可被ResponseDelivery处理的NetworkResponse。
**Cache**：缓存请求结果，Volley 默认使用的是基于 sdcard 的DiskBasedCache。NetworkDispatcher得到请求结果后判断是否需要存储在 Cache，CacheDispatcher会从 Cache 中取缓存结果。


**Volley 请求流程图**
![](https://raw.githubusercontent.com/android-cn/android-open-project-analysis/master/tool-lib/network/volley/image/Volley-run-flow-chart.png)