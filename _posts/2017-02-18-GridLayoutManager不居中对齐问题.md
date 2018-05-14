--
layout:     post                            # 使用的布局（不需要改）
title:      GridLayoutManager 不居中对齐问题             # 标题
subtitle:   item布局使用android:layout_width="match_parent"   #副标题
date:       2017-02-18                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


#### 前言

在项目中遇到用GridLayoutManager做底部tab标签，发现每个item不会居中，在item中设置LinearLayout的android:orientation="vertical"也还是没用。


#### 解决方案

把item布局的android:layout_width="wrap_content"改成
```
android:layout_width="match_parent"
```

若需要间距，就配置一下ItemDecoration
```
class MarginDecoration extends RecyclerView.ItemDecoration {
    private int margin;

    public MarginDecoration(Context context) {
        margin = PxUtils.dpToPx(10,context);
    }

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        outRect.set(margin, margin, margin, margin);
    }
}
```