--
layout:     post                            # 使用的布局（不需要改）
title:      Android BLE中心和外设通信时中心设备的onCharacteristicChanged()没有回调              # 标题
subtitle:   setCharacteristicNotification、notifyCharacteristicChanged   #副标题
date:       2017-10-27                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android ble
---


## Android BLE 中心和外设通信时 中心设备的onCharacteristicChanged() 没有回调

今天为实现中心设备可以从外设中读取大数据的功能，想到一种方法，但是遇到了onCharacteristicChanged()一直收不到回调的问题：
经过研究发现了如下几个可能导致该问题的点：
1. 在创建某个特征值A时，一定记得设置该特征值为PROPERTY_NOTIFY；例如：如下标红位置
```
A = new BluetoothGattCharacteristic(UUID.fromString(NOTICE), BluetoothGattCharacteristic.PROPERTY_WRITE|
BluetoothGattCharacteristic.PROPERTY_READ|BluetoothGattCharacteristic.PROPERTY_NOTIFY, BluetoothGattCharacteristic.PERMISSION_WRITE|BluetoothGattCharacteristic.PERMISSION_READ);
```
2. 在中心设备上在发现A特征值时要设置：bluetoothGatt.setCharacteristicNotification(A,true);表示该特征值可以接收通知；
3. 在外设中，当A的值改变时要通过bluetoothGattServer.notifyCharacteristicChanged(device,A,false)发送通知；可以在onNotificationSend中查看通知是否发送成功；
4. 最后在中心设备的onCharacteristicChanged中查看是否有回调；

如果以上几个步骤都完成的话，应该是可以收到回调的，若再收不到回调，则可能是第2步设置的有问题，可以查看一下第2步是否返回true，依次查看后面几步是否有问题；