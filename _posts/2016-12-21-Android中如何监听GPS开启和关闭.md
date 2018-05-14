--
layout:     post                            # 使用的布局（不需要改）
title:      Android中如何监听GPS开启和关闭             # 标题
subtitle:      #副标题
date:       2016-12-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


有时需要监听GPS的开关(这种需求并不多见)。实现的思路是监听代表 GPS 开关的 URI 的数据是否有发生变化

GPS的开关是系统设置，android.provider.Settings.Secure 代表了app可以读取但不能修改的系统设置。可以从这个类中获取到代表GPS开关的URI。

关键代码如下：
```
getContentResolver()
            .registerContentObserver(
                    Settings.Secure
                            .getUriFor(Settings.System.LOCATION_PROVIDERS_ALLOWED),
                    false, mGpsMonitor);
```

完整的代码如下：
```
public class MyActivity extends Activity {

    private final ContentObserver mGpsMonitor = new ContentObserver(null) {
        @Override
        public void onChange(boolean selfChange) {
            super.onChange(selfChange);

            boolean enabled = mLocationManager
                    .isProviderEnabled(LocationManager.GPS_PROVIDER);
            System.out.println("gps enabled? " + enabled);
        }
    };

    private LocationManager mLocationManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mLocationManager = (LocationManager) getSystemService(LOCATION_SERVICE);
    }

    protected void onStart() {
        getContentResolver()
                .registerContentObserver(
                        Settings.Secure
                                .getUriFor(Settings.System.LOCATION_PROVIDERS_ALLOWED),
                        false, mGpsMonitor);
    };

    @Override
    protected void onStop() {
        super.onStop();
        getContentResolver().unregisterContentObserver(mGpsMonitor);
    }
}
```