---
layout:     post                            # 使用的布局（不需要改）
title:      Android编程之LayoutInflater的inflate方法详解                   # 标题
subtitle:            #副标题
date:       2015-10-27                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


### Android编程之LayoutInflater的inflate方法详解

`LayoutInflater` 的 `inflate` 方法，在 `fragment` 的 `onCreateView` 方法中经常用到：
```
public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) { 
} 
```

`LayoutInflater` 的 `inflate` 方法一共有四种，但我们日常用经常用到的就只有这两种：
```
public View inflate(int resource, ViewGroup root) {  
    return inflate(resource, root, root != null);  
}
```

```
public View inflate(int resource, ViewGroup root, boolean attachToRoot) {  
    if (DEBUG) System.out.println("INFLATING from resource: " + resource);  
    XmlResourceParser parser = getContext().getResources().getLayout(resource);  
    try {  
        return inflate(parser, root, attachToRoot);  
    } finally {  
        parser.close();  
    }  
}  
```

所以，这里直接介绍里面调用这个方法即可：
```
public View inflate(XmlPullParser parser, ViewGroup root, boolean attachToRoot)
```

在这个方法里面，上半部分为xml解析的代码，这里就不贴出来，确实没什么东西可看。直接看中间部分的代码：
```
ViewGroup.LayoutParams params = null;  
if (root != null) {  
    if (DEBUG) {  
        System.out.println("Creating params from root: " + root);  
    }  
    // Create layout params that match root, if supplied  
    params = root.generateLayoutParams(attrs);  
    if (!attachToRoot) {  
        // Set the layout params for temp if we are not  
        // attaching. (If we are, we use addView, below)  
        temp.setLayoutParams(params);  
    }  
}  
```

params = root.generateLayoutParams(attrs);

这段的意思是：如果调用 `inflate` 方法，传入了 `ViewGroup root` 参数，则会从 `root` 中得到由 `layout_width` 和 `layout_height` 组成的 `LayoutParams` ，在 `attachToRoot` 设置为 `false` 的话，就会对我们加载的视图 `View` 设置该 `LayoutParams` 。

接着往下看：
```
// We are supposed to attach all the views we found (int temp)  
// to root. Do that now.  
if (root != null && attachToRoot) {  
    root.addView(temp, params);  
}  
  
// Decide whether to return the root that was passed in or the  
// top view found in xml.  
if (root == null || !attachToRoot) {  
    result = temp;  
}  
```
root.addView(temp, params);

如果设置了 `ViewGroup root` 参数，且 `attachToRoot` 设置为 `true` 的话，则将我们加载的视图做为子视图添加到root视图中。



如果我们ViewGroup root设置为空的话，就直接返回我们创建的视图；如果root不为空，且attachToRoot设置为false的话，就返回上面那段：对我们加载的视图View设置该LayoutParams。