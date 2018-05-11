--
layout:     post                            # 使用的布局（不需要改）
title:      Android：将一个Activity、某块布局转换成图片              # 标题
subtitle:   启用View自带绘制缓存保存图片   #副标题
date:       2017-06-28                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

#### 使用截屏，然后保存图片
	
1. 如果要保存的布局正好是撑满这个屏幕，那可以使用截屏的方式来保存图片；
2. 如果只是屏幕的某一个部分，那才用截屏后，还得对截屏后的图片进行二次裁剪，而且要裁剪的坐标点也比较难正确得出；
3. 要保存的布局大于当前屏幕，那就得多次截屏，然后再把几个截屏拼接起来，费时费力。


#### 启用View自带绘制缓存(推荐)
	
Google估计早就想到了这个问题，在查看View的源码的时候，发现View本身有两个方法很有意思。
一是：setDrawingCacheEnabled(boolean enabled)，这个方法设置为true，就可以生成位图的副本（原话：This API can be used to manually generate a bitmap copy of this view, by setting the flag to true and calling {@link #getDrawingCache()}.）；
二是：getDrawingCache()，这个方法其实是调用的getDrawingCache(false)，这个方法返回的一个绘制此View时的视图缓存Bitmap（原话：Returns the bitmap in which this view drawing is cached.）；
知道了这两个方法就可以将任意XML任意模块的View给生成图片，是不是很简单，如下：

1. 父布局（要生成图片的布局，下边同）开启绘图缓存；
2. 计算父布局宽高；
3. 父布局设置layout；
4. 用Bitmap.createBitmap(获取父布局到的cache)生成Bitmap；
5. 关闭DrawingCache；
6. 接下来是保存到本地还是直接设置给ImageView自己决定了。

```
ll.setDrawingCacheEnabled(true);
ll.measure(View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED),
View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED));
ll.layout(0, 0, ll.getMeasuredWidth(), ll.getMeasuredHeight());
Bitmap bitmap = Bitmap.createBitmap(ll.getDrawingCache());
ll.setDrawingCacheEnabled(false);
iv.setImageBitmap(bitmap);
```