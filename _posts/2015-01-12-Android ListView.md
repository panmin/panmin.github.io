---
layout:     post                            # 使用的布局（不需要改）
title:      Android ListView                  # 标题
subtitle:           #副标题
date:       2015-01-12                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android ListView

1. 作用：android系统中显示列表的控件
2. 使用：
     需要数据源以及数据适配器；（数据适配器是连接数据源和视图界面的桥梁）
     实现过程：新建适配器→添加数据源到适配器→视图加载数据适配器
     Demo1(简单列表格式):
     第一步：通过findViewById获取控件xml中的控件，
      新建数据源 String[] datasource={"item1","item2","item3","item4","item5"};
     第二步：新建数据适配器：ArrayAdapter arrayAdapter=new ArrayAdapter(this,android.R.layout. simple_list_item_1,datasource);
     第三步：视图加载数据适配器：listview.setAdapter(arrayAdapter);
     Demo2(可以自定义ListView中的项的样式):
     第一步：通过findViewById获取xml中的控件，
     新建数据源：List<Map<String,Object>> list=new ArrayList<Map<String,Object>>();
     注：Android中的Map相当于C#中的KeyValuePair(Dictionary中的某一行)，
	```
	Map<String,Object> map=new HashMap<String,Object>();
	map.put("key1","value1");
	map.put("key2","value2");
	list.add(map);
	```
     第二步：新建xml作为ListView的Item,
	```
    <ImageView
        android:id="@+id/img"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_launcher"
        />
    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="测试"/>
	```
     新建数据适配器：
	```
    simpleAdapter =new SimpleAdapter( this,list , R.layout.simpleitem, new String[]{"img" ,"text"}, new int[]{R.id.img,R.id. text});
	```
     第三部：视图加载适配器
	```
    listView.setAdapter(simpleAdapter);
	```
3. ListView列表点击事件
	```
    listView.setOnItemClickListener(new OnItemClickListener() {
      @Override
      public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,long arg3) {
           TextView tv=(TextView) arg1.findViewById(R.id. text);
           Toast. makeText(simpleAdapterMain.this, tv.getText(), Toast. LENGTH_SHORT).show();
      }
    });
	```
4. ListView添加滚动事件监听
	```
    listView.setOnScrollListener(new OnScrollListener() {
          @Override
          public void onScrollStateChanged(AbsListView view, int scrollState) {
               switch (scrollState) {
                //用户手指在离开屏幕之前，由于用力的划了一下，视图仍惯性滑动
                case SCROLL_STATE_FLING :
                       break;
                //已经停止滑动
                case SCROLL_STATE_IDLE :
                       break;
                //手指没有离开屏幕，视图正在滑动
                case SCROLL_STATE_TOUCH_SCROLL :
                       break;
                default:
                       break;
               }
         }
         @Override
         public void onScroll(AbsListView view, int firstVisibleItem, int visibleItemCount, int totalItemCount) {
               // TODO Auto-generated method stub
         }
    });
	```
当数据源的内容改变之后，需要用数据适配器来通知界面刷新
```
simpleAdapter .notifyDataSetChanged();
```