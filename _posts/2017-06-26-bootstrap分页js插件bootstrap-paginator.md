---
layout:     post                            # 使用的布局（不需要改）
title:      bootstrap分页js插件bootstrap-paginator                   # 标题
subtitle:   bootstrap3修改了关于pagination在bootstrap2中的部分样式，且bootstrap3中添加分页功能是以ul为根节点添加的  #副标题
date:       2017-06-26                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - html
---

# bootstrap分页js插件bootstrap-paginator

## 使用bower来下载js插件
```
npm install -g bower  //安装bower

bower install bootstrap-paginator //下载分页插件

bower install bootstrap //下载bootstrap的css和js
```

## bootstrap-paginator的使用
* 使用bootstrap-paginator中带的bootstrap.css的用法是：

```
<!--css和js的引用-->
<link rel="stylesheet" href="/css/bootstrap.min.css">
<script src="/js/jquery-1.9.1.min.js"></script>
<script src="/js/bootstrap-paginator.min.js"></script>

<!--分页控件的位置-->
<div id="page" totalPages="<%=page.totalPages%>" currentPage="<%=page.currentPage %>"></div>


<!--初始分页控件-->
<script type="text/javascript>
	$(function(){
        //分页功能
        var page = $('#page');
        var options = {
            currentPage: page.attr('currentPage'),
            totalPages: page.attr('totalPages'),
            onPageClicked: function(e,originalEvent,type,pageNum){
                console.log("Page item clicked, type: "+type+" page: "+pageNum+"----");
            }
        }
        page.bootstrapPaginator(options);
    })
</script>
```
`bower下载下来的bootstrap-paginator中带的bootstrap.css是2.x版本的，用bower下载的bootstrap是3.x版本的是不兼容的`

* 使用bower下载的bootstrap.css
> 需要修改bootstrap-paginator源代码才能兼容，bootstrap3修改了关于pagination在bootstrap2中的部分样式，且bootstrap3中添加分页功能是以ul为根节点添加的。
	
	1. 在html中将分页控件的占位符从div改成ul
	```
	<ul id="page" totalPages="<%=page.totalPages%>" currentPage="<%=page.currentPage %>"></ul>
	```
	2. 修改bootstrap-paginator.js中的源代码
	找到**bootstrapMajorVersion: 2**，改成**bootstrapMajorVersion: 3**就行了