---
layout:     post                            # 使用的布局（不需要改）
title:      Android中startActivity和startActivityForResult                  # 标题
subtitle:           #副标题
date:       2015-06-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---

## Android中startActivity和startActivityForResult
1. startActivity：打开activity，但不需要返回值
	```
    Intent intent = new Intent(this , TestActivity.class);
    startActivity(intent);
	```
2. startActivityForResult：打开activity，并需要activity给返回数据，需要在主activity中重写onActivityResult方法。
	```
	Intent intent1 = new Intent( this, TestActivity.class);
    startActivityForResult(intent1, 0);
	```

	```
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		switch (resultCode) { //resultCode为回传的标记，我在B中回传的是RESULT_OK
		   case RESULT_OK:
		    Bundle b=data.getExtras(); //data为B中回传的Intent
		    String str=b.getString("str1");//str即为回传的值
		    break;
		default:
		    break;
		    }
		}
	```

注：如果在startActivityForResult起来的Activity里面设置setResult,结果并不会马上返回给parent的Activity,只有当前Activity被finish,结果才会被发送给parent的onActivityResult去处理!

```
Intent data = new Intent();
data.putExtra("key", "aaa");
setResult(1, data);
//setResult(0);
```