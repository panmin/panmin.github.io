---
layout:     post                            # 使用的布局（不需要改）
title:      Android性能优化：ViewStub                   # 标题
subtitle:            #副标题
date:       2015-10-12                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android性能优化：ViewStub

### 前言
ViewStub是Android布局优化中一个很不错的标签/控件，直接继承自View。虽然Android开发人员基本上都听说过，但是真正用的可能不多。

ViewStub可以理解成一个非常轻量级的View，与其他的控件一样，有着自己的属性及特定的方法。当ViewStub使用在布局文件中时，当程序inflate布局文件时，ViewStub本身也会被解析，且占据内存控件


### 与其他控件相比，主要区别体现在以下几点

1. 当布局文件 `inflate` 时，`ViewStub` 控件虽然也占据内存，但是相相比于其他控件，ViewStub所**占内存很小**；
2. 当布局文件 `inflate` 时，`ViewStub` 主要是作为一个**“占位符”**的性质，放置于view tree中，且ViewStub本身是不可见的。ViewStub中有一个layout属性，指向ViewStub本身可能被替换掉的布局文件，在一定时机时，**通过viewStub.inflate()完成此过程**；
3. `ViewStub` 本身是**不可见**的，对ViewStub setVisibility(..)与其他控件不一样，ViewStub的setVisibility 成View.VISIBLE或INVISIBLE如果是*首次使用*，都会*自动inflate其指向的布局文件*，并*替换ViewStub本身*，再次使用则是相当于对其指向的布局文件设置可见性。


### 这里需要注意的是：

1. `ViewStub` 之所以常称之为**“延迟化加载”**，是因为在教多数情况下，程序无需显示ViewStub所指向的布局文件，只有在特定的某些较少条件下，此时ViewStub所指向的布局文件才需要被inflate，且此布局文件直接将当前ViewStub替换掉，具体是通过**viewStub.infalte()**或**viewStub.setVisibility(View.VISIBLE)**来完成；
2. 正确把握住ViewStub的应用场景非常重要，正如如1中所描述需求场景下，使用ViewStub可以优化布局；
3. 对 `ViewStub` 的 `inflate` 操作**只能进行一次**，因为inflate的时候是将其指向的布局文件解析inflate并替换掉当前ViewStub本身（由此体现出了ViewStub“占位符”性质），一旦替换后，此时原来的布局文件中就没有ViewStub控件了，因此，*如果多次对ViewStub进行infalte，会出现错误信息：ViewStub must have a non-null ViewGroup viewParent*。
4. 3中所讲到的ViewStub指向的布局文件解析inflate并替换掉当前ViewStub本身，并不是完全意义上的替换（与include标签还不太一样），替换时，布局文件的layout params是以ViewStub为准，其他布局属性是以布局文件自身为准。



下面看一下简单的需求场景：在listview显示列表数据时，可能会出现服务端一条数据都没有的情况，此时显示一个EmptyView，提示用户暂无数据。此时考虑到实际应用中EmptyView显示出来的机会相当小，因此，可以在布局文件中使用ViewStub站位，然后确实没有数据时才viewStub.infalte()。

相关部分代码如下：
```
public void showEmptyView() {
    listview.setVisibility(View.GONE);
    if (noDataView == null) {
        ViewStub noDataViewStub = (ViewStub) view.findViewById(R.id.no_data_viewstub);
        noDataView = noDataViewStub.inflate();
    } else {
        noDataView.setVisibility(View.VISIBLE);
    }
}

public void showListView(){
    listview.setVisibility(View.VISIBLE);
    if(noDataView != null){
        noDataView.setVisibility(View.GONE);
    }
}
```

特别需要注意的是对ViewStub是否已经inflate的判断。

在Listview Item中，有时候可能遇到如下场景：在不同的列表页item的布局一部分不同，但相对于整个item布局来说又不是很多，此时最常见的有如下两种处理：
1. 对不同的部分都写出来，放到一个item文件中，然后逻辑分别处理不同部分的显示与否（View.VISIBLE和View.GONE）；
2. 对这两种不同的item整个部分都分别区分开，完全写成两个item文件，然后结合listView显示不同布局分别做逻辑处理（通过getItemType()等方式）。

以上两种处理方式其实都可以，第一种方式逻辑清晰，非常灵活，只是在一定程度上增加了内存和资源消耗。第二种方式是的布局文件有重复（虽然相同部分可以通过include，但是逻辑上还是有重复的），包括逻辑上处理的代码实质上的重复。一般对于有较大不同的item布局推荐采用此种方式。

有时候结合需求，可以在第一种方式的基础上，结合ViewStub“占位符”可以比较好的完成此类需求。也相当于是两种方式的一种折中形式，但同时兼顾了内存和资源消耗以及不同的布局逻辑控件。