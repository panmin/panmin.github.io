--
layout:     post                            # 使用的布局（不需要改）
title:      Android中GreenDao的缓存问题             # 标题
subtitle:      #副标题
date:       2016-08-03                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


## Android GreenDao - Delete cached objects of a specific entity only

```
DaoSession.clear();//清除所有数据库表的缓存
dao.detachAll()；//清除某个表的所有缓存
```
use yourDao.detach(yourEntity) to remove a single entity from the identity scope. This forces subsequent queries for this database object to return a different object.

```
DaoMaster dm = AppContext.getDaoMaster(ctx);

DaoSession ds = dm.newSession(IdentityScopeType.Session);
```
&emsp;&emsp;如果传入的是IdentityScopeType.Session，同样的查询只会执行一次，查询结果对象List保存在内存里会重复使用。会导致的问题是如果List对象属性改变，未持久化它，再次做query,不会从数据库查询，只是缓存中的结果。会导致与数据库表数据不一致。

&emsp;&emsp;解决办法，再次执行查询之前执行ds.clear();方法。或者在这种情况下之不保留缓存，用**IdentityScopeType.None**。

```
public List<Label> queryLabels(){  
    DaoSession ds = AppContext.getDaoSession(ctx);  
	//clear the cache,requery the data from database table, not from the memory.  
    ds.clear();  
      
    QueryBuilder<Label> qb = ds.getLabelDao().queryBuilder();  
    qb.where(LabelDao.Properties.Deleted.eq(false));  
    List<Label> labels = qb.list();  
    if(labels.size()<showNum){  
        Label entity = new Label(null, "添加标签", null, false, null, null, true, false);  
        labels.add(entity);  
    }  
    return labels;  
}  
```

或者用IdentityScopeType.None创建DaoSession

```
private DaoSession getDaoSession(IdentityScopeType scope){  
    if(scope == IdentityScopeType.Session){  
        DaoSession ds = AppContext.getDaoSession(ctx);  
        return ds;  
    }else{  
        DaoMaster dm = AppContext.getDaoMaster(ctx);  
        DaoSession ds = dm.newSession(IdentityScopeType.None);  
        return ds;  
    }  
}  
```