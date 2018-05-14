--
layout:     post                            # 使用的布局（不需要改）
title:      Android中setOnTouchEvent设置返回值为true和false的区别             # 标题
subtitle:      #副标题
date:       2016-11-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


android onTouchEvent返回true时 setOnclickListener 不触发


今天在做自定义的可选文本的 TextView 类时，用到了 View 类的 setOnTouchListener（OnTouchListener l）事件监听,在构造 OnTouchListener 对象时需要重写 onTouch（...）方法，这个方法的返回值是布尔类型，
 
刚开始我按默认给定的返回值 false运行，发现只能监听到这个 view 的“按下”事件，“移动”和“抬起”都不能够监听到；于是把返回值改为了 true，就都监听到了。
但是随后又遇到了一个问题，那就是：点击其他组件后，再点击这个 view 的时候获取不到焦点！也就是click事件不响应了。我猜想一定是受到了 onTouch 方法的影响，查资料发现：
    
  **设为true 和 false的效果是有区别的：**

  setOnTouchListener 单独使用的时候返回值需要为true，这样才能保证移动的时候能后获取相应的监
  听，而非一次监听（即每次只有一个按下的事件）
  setOnTouchListener 和 setOnClickListener 同时使用时，onTouch 的返回值要设为 false，这样既可
  以保证按下移动抬起事件可以被监听，并且点击事件也会被监听。
 

于是，我又对这个 view 设置了setOnClickListener（虽然重写的方法体中什么也没写），并将onTouch 的返回值要设为 了false，结果就 既监听到了Touch 也能监听到 click 事件了，可以重获焦点了！

```
tv.setOnTouchListener(new OnTouchListener() {
            
    public boolean onTouch(View v, MotionEvent event) {
    // TODO Auto-generated method stub
          
    switch (event.getAction()) {
      case MotionEvent.ACTION_DOWN:
      ...   
      break;

      case MotionEvent.ACTION_MOVE:
      ...
      break;

      case MotionEvent.ACTION_UP:     
      ...
      break;
    }

    return false; // (或return  true）

   }
  });
```