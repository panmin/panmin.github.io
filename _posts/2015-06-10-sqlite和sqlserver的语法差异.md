---
layout:     post                            # 使用的布局（不需要改）
title:      sqlite和sqlserver的语法差异                  # 标题
subtitle:           #副标题
date:       2015-06-10                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - 数据库
---


## sqlite和sqlserver的语法差异

1. top n
mssql： select top 2 \* from table
sqlite：select \* from table limit 2

2. exists
mssql：
```
if not exists (select \* from table where id=5)
               begin
             insert into table(field1)
               select 't'
               end
```
sqlite：
```
insert into table(field1) select 't' where not exists(select \* from table where id=5)
```

3. 嵌套事务
     sqlite仅允许单个活动的事务
4. right和full outer join
     sqite不支持right和full outer join
5. 可更新的视图
     sqlite视图是只读的，不能对视图执行delete\insert\update操作
     mssql是可以对视图执行delete\insert\update操作的。