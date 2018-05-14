--
layout:     post                            # 使用的布局（不需要改）
title:      Android studio自定义class模版             # 标题
subtitle:      #副标题
date:       2016-01-22                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


#### MySingleton（单例模版）：
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end

import android.content.Context;

#parse("File Header.java")
public class ${NAME}{
    private static final String TAG = "${NAME}";
    private Context mContext;
    private ${NAME}(Context context) {
        this.mContext = context;
    }
    private static ${NAME} instance;

    public static ${NAME} getInstance(Context context) {
        if(instance == null){
            synchronized (${NAME}.class){
                if(instance == null){
                    instance = new ${NAME}(context.getApplicationContext());
                }
            }
        }
        return instance;
    }

}
```


2016一数APP项目DBHelper模版
**MyDBHelper:**
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end

import android.content.Context;
import com.yishu.controller.db.DBManager;
import com.yishu.model.${NAME};

import java.util.List;

#parse("File Header.java")
public class ${NAME}DBHelper{
    private static final String TAG = "${NAME}DBHelper";
    private Context mContext;
    private DBManager<${NAME},Long> mDBManager;
    public ${NAME}DBHelper(Context context) {
        this.mContext = context.getApplicationContext();
        mDBManager = new DBManager(mContext,${NAME}.class);
    }
   
    public void save(${NAME} item) {
        mDBManager.save(item);
    }

    public void save(${NAME}... items) {
        mDBManager.save(items);
    }

    public void save(List<${NAME}> items) {
        mDBManager.save(items);
    }

    public void saveOrUpdate(${NAME} item) {
        mDBManager.saveOrUpdate(item);
    }

    public void saveOrUpdate(${NAME}... items) {
        mDBManager.saveOrUpdate(items);
    }

    public void saveOrUpdate(List<${NAME}> items) {
        mDBManager.saveOrUpdate(items);
    }

    public void deleteByKey(Long key) {
        mDBManager.deleteByKey(key);
    }

    public void delete(${NAME} item) {
        mDBManager.delete(item);
    }

    public void delete(${NAME}... items) {
        mDBManager.delete(items);
    }

    public void delete(List<${NAME}> items) {
        mDBManager.delete(items);
    }

    public void deleteAll() {
        mDBManager.deleteAll();
    }

    public void update(${NAME} item) {
        mDBManager.update(item);
    }

    public void update(${NAME}... items) {
        mDBManager.update(items);
    }

    public void update(List<${NAME}> items) {
        mDBManager.update(items);
    }

    public ${NAME} query(Long key) {
        return mDBManager.query(key);
    }

    public List<${NAME}> queryAll() {
        return mDBManager.queryAll();
    }

    public List<${NAME}> query(String where, String... params) {
        return mDBManager.query(where, params);
    }

    public long count() {
        return mDBManager.count();
    }

    public void refresh(${NAME} item) {
        mDBManager.refresh(item);
    }

    public void detach(${NAME} item) {
        mDBManager.detach(item);
    }
}
```