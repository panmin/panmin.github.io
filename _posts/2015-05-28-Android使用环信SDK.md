---
layout:     post                            # 使用的布局（不需要改）
title:      Android使用环信SDK                  # 标题
subtitle:           #副标题
date:       2015-05-28                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android使用环信SDK

1. AndroidManifest.xml解析
	```
	android:windowSoftInputMode="adjustPan" 
	```
	activity的屏幕大小并不会调整来保证软键盘的空间，而是采取了另外一种策略，系统会通过布局的移动，来保证用户要进行输入的输入框肯定在用户的视野范围内，从而让用户可以看到自己输入的内容。
	
	```
	android:screenOrientation="portrait"
	```
	是限制此页面是竖屏显示

2. 工具类
	CommonUtils.isNetWorkConnected(this) 检测网络是否可用
	```
	final ProgressDialog pd = new ProgressDialog(LoginActivity.this);
    pd.setCanceledOnTouchOutside( false);
    pd.setOnCancelListener( new OnCancelListener() {

          @Override
          public void onCancel(DialogInterface dialog) {
                progressShow = false ;
         }
    });
    pd.setMessage("正在登录中...");
    pd.show();//显示
    pd.dismiss();//取消  
	```
	
	```
	String username ="";
    TextUtils.isEmpty(username)
	```

	```
	runOnUiThread(new Runnable() {
     	public void run() {
        }
    });
	```

	汉字转拼音，搜索时匹配用到:
	```
	HanziToPinyin.getInstance().get(headerName.substring(0, 1)).get(0).target.substring(0, 1).toUpperCase()
	```

	```
	public class ChatAllHistoryFragment extends Fragment//继承Fragment
    public class ContactlistFragment extends Fragment
    public class SettingsFragment extends Fragment
    //将Fragment添加到主界面
    getSupportFragmentManager().beginTransaction().add(R.id. fragment_container, chatHistoryFragment)
                       .add(R.id. fragment_container, contactListFragment).hide(contactListFragment)
					   .show(chatHistoryFragment )
                       .commit();
	```