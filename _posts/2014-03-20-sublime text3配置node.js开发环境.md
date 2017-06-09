---
layout:     post                            # 使用的布局（不需要改）
title:      sublime text3配置node.js开发环境                   # 标题
subtitle:   sublime text2到3的更新  #副标题
date:       2014-03-20                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - node.js
---

# sublime text3配置node.js开发环境

## 下载sublime text3
在官网下载就行了

## 配置node.js开发环境
> sublime text3菜单→tools→install control

快捷键ctrl+shift+P调出control package界面输入nodejs,然后回车，会安装node.js插件；
Preferences→Browser Package打开安装nodejs插件的文件夹，
**Nodejs.sublime-build**文件修改：
```
"encoding": "utf8"
```

**Nodejs.sublime-settings**文件修改：
```
"node_command": "D:\\Program Files\\nodejs\\node.exe",
"npm_command": "D:\\Program Files\\nodejs\\npm.cmd",
```

按`alt+r`就能执行node代码了
 