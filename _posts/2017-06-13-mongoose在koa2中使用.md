---
layout:     post                            # 使用的布局（不需要改）
title:      mongoose在koa2中使用                   # 标题
subtitle:     #副标题
date:       2017-06-13                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - node.js
---


# mongoose在koa2中使用

## mongoose安装
```
npm install mongoose --save
```

## mongoose在koa2中使用
```
router.get('/getusers',async (ctx,next)=>{
	var users =await User.find({})
	await ctx.render({'users':users})
})
```