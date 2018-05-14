--
layout:     post                            # 使用的布局（不需要改）
title:      Android中Handler造成的Context泄漏及解决方法             # 标题
subtitle:      #副标题
date:       2016-09-19                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


在Activity中定义一个非静态的内部Handler类时，Lint会报一个内存泄漏的警告；这是因为在ADT 20中Lint增加了一个检查项目，即确保Handler内部类不包含对外部类的隐式引用。在Java中，非静态内部类（包括匿名内部类）会持有一个隐式的外部类引用，而静态内部类不会引用外部类对象。 内存泄漏会造成很多问题，导致手机上面的硬件资源被毫无意义的浪费，所以我查了些资料，简单分析了下Handler造成内存泄漏的原因，以及如何避免发生这样的内存泄漏。

考虑如下代码：
```
public class MainActivity extends Activity {
    Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    };
}
```

此时运行Android Lint工具会有一个内存泄漏的警告（大概翻译如下）：
> 这个Handler类应该定义成静态的，否则可能会发生内存泄漏；
> 确保这个Handler类没有持有一个外部类的引用；
> 因为这个Handler类被定义成内部类，它可能会阻止外部类被“垃圾回收”。如果Handler类正在使用一个外部线程的Looper或MessageQueue对象，那就不会发生问题。如果Handler正在使用主线程中的Looper或MessageQueue对象，你需要修改Handler的定义：把Handler类定义成静态类或者在实例化Handler的时候传入外部类的弱引用对象，所有对外部类成员的访问都通过这个弱引用对象。


当Android应用启动的时候，框架层会先创建一个应用主线程的Looper对象，Looper实现了一个简单的消息队列，循环不断的处理队列上的消息对象。主线程的Looper对象在整个应用生命周期中都存在。

Handler类构造方法的API文档中这样一句话： 初始化Handler时，该Handler对象和当前线程中的Looper对象关联；当在主线程线程中初始化Handler时就会和主线程中的Looper对象关联。

下面这段代码是Handler发送的Message进入消息队列时必须调用的方法：
```
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this; // 这个Message引用了Handler对象
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);
}
```

可以看到发送到消息队列的Message对象持有一个发送该消息的Handler的引用，这样系统就可以调用Handler#handleMessage()方法来分发处理该消息。

那么泄漏发生在哪里呢？考虑下面一个例子：
```
public class MainActivity extends Activity {
    Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    };
        
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        mLeakyHandler.postDelayed(new Runnable() {  
            public void run() { }  
        }, 100000);  

        finish();  
    }
}
```

当Activity被finish之后，我们发出的那个延时消息将在主线程的消息队列中保持10分钟，直到该消息最终被处理，由于消息持有一个Handler引用，而Handler又持有一个它的外部类MainActivity的引用，这样就阻止了Activity被“垃圾回收”，从而泄漏了Activity引用的所有应用资源，注意上述例子中的匿名的Runnable对象也一样造成了Activity的泄漏。


一般来说，这种内存泄漏的情况通常不会发生，除非你发送了一个延时很长的消息。

我在网上看到这样的解决方法：“把Handler类定义成静态类，然后用post方法把Runnable对象传送到主线程”。

显然这种方式是不能阻止内存泄漏的，把Handler定义成静态的，虽然能阻止Handler持有外部Activity的引用，但是使用post方式发送的匿名内部Runnable类还是会持有外部Activity的引用，而且Runnable最终还是被封装成Message对象，只要这个Message还在消息队列中，该Activity就无法被垃圾回收。（除非使用静态Runnable对象）

注意：当你在Activity中使用内部类的时候，需要时刻考虑你是否可以控制该内部类的生命周期，如果不可以，则最好定义为静态内部类。



将Handler改为静态内部类，如果需要在Handler中调用Activity中的方法，可以在Handler中使用一个WeakReference来持有Activity的弱引用。

参考如下代码：
```
static class MyHander extends Handler {
    private final WeakReference<Activity> mActivity;

    public MyHander(Activity activity) {
        mActivity = new WeakReference<Activity>(activity);
    }

    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        if (mActivity.get() != null) {
            // ...
        }
    }
}
```
WeakReference类似于可有可无的东西。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不会很快发现那些具有弱引用的对象。

如果你不想每次都创建一个WeakReference，可以创建这样一个通用类：
```
public abstract class WeakReferenceHandler<T> extends Handler {
    private WeakReference<T> mReference;

    public WeakReferenceHandler(T reference) {
        mReference = new WeakReference<T>(reference);
    }

    @Override
    public void handleMessage(Message msg) {
        if (mReference.get() == null) {
            return;
        }
        handleMessage(mReference.get(), msg);
    }

    protected abstract void handleMessage(T reference, Message msg);
}
```