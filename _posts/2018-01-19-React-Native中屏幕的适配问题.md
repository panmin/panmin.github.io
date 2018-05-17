--
layout:     post                            # 使用的布局（不需要改）
title:      React-Native中屏幕的适配问题              # 标题
subtitle:   Dimension的使用   #副标题
date:       2018-01-19                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - React-Native
---


## React-Native中屏幕的适配问题

> React Native不同设备分辨率适配和设计稿尺寸单位px的适配，设计师给的稿子都是按照px像素单位设计的，这个时候我们怎么快速还原设计稿那？
> React Native中使用的尺寸单位是dp(一种基于屏幕密度的抽象单位。在每英寸160点的显示器上，1dp = 1px),而设计师使用的是px, 这两种尺寸如何换算呢？

### 官方提供了PixelRatio进行pt到px的转换

```
import {PixelRatio} from 'react-native';
const dp2px = dp=>PixelRatio.getPixelSizeForLayoutSize(dp);
const px2dp = px=>PixelRatio.roundToNearestPixel(px);
```

设计师给你一个尺寸，比如100px*200px的View，按照下面的方式可实现设计还原：View 
```
width:px2dp(100),height:px2dp(200),backgroundColor:"red"
```

如果每个地方都这样写会很麻烦，于是我们就有了下面转换代码
```
import {PixelRatio,Dimensions}} from 'react-native';

const dp2px = dp=>PixelRatio.getPixelSizeForLayoutSize(dp);
const px2dp = px=>PixelRatio.roundToNearestPixel(px);

let designSize = {width:720,height:1280}; //假设设计尺寸为：720*1280

let pxRatio = PixelRatio.get();

let win_width = Dimensions.get("window").width;

let win_height = Dimensions.get("window").height;

let width = dp2px(win_width);

let height = dp2px(win_height);

let design_scale = designSize.width/width;

height = height*design_scale

let scale = 1/pxRatio/design_scale;

```

**实际使用：**
要在最外层View上设置如下样式：
```
const styles = StyleSheet.create({
    container: {
        width: width,
        height: height,
        transform: [{translateX: -width * .5}, {translateY: -height * .5}, {scale: scale}, {translateX: width * .5}, {translateY: height * .5}]
    }
});
```

在后续的开发中将不必再关注适配的问题，只需要按照设计师给的尺寸实现布局即可，比如设计稿中的图片尺寸是300px*300px，你的样式中就写 width: 300,height: 300,
```
//这里就写设计稿上的300px 300px，不用带单位
image: {
        width:300,
        height:300
    }
```

