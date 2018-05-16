---
layout:     post                            # 使用的布局（不需要改）
title:      Android BLE中Peripheral端和Central端                  # 标题
subtitle:           #副标题
date:       2015-08-04                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---



## Android BLE中Peripheral端和客户端

### 角色和职责

* Android设备与BLE设备交互有两组角色：

	中心设备和外围设备（Central vs. peripheral）；
	GATT server vs. GATT client.

* Central vs. peripheral:

	中心设备和外围设备的概念针对的是BLE连接本身。Central角色负责scan advertisement。而peripheral角色负责make advertisement。

这两种角色取决于BLE连接成功后，两个设备间通信的方式。



**举例说明：**
&emsp;&emsp;现有一个活动追踪的BLE设备和一个支持BLE的Android设备。Android设备支持Central角色，而BLE设备支持peripheral角色。创建一个BLE连接需要这两个角色都存在，都仅支持Central角色或者都仅支持peripheral角色则无法建立连接。

&emsp;&emsp;当连接建立后，它们之间就需要传输GATT数据。谁做server，谁做client，则取决于具体数据传输的情况。例如，如果活动追踪的BLE设备需要向 Android设备传输sensor数据，则活动追踪器自然成为了server端；而如果活动追踪器需要从Android设备获取更新信息，则 Android设备作为server端可能更合适。


**首先说明一点，并不是Android L的系统就可以支持BLE Peripheral，这个和硬件也是有关系的（以前有人告诉我支持BLE Peripheral是纯软件的东西，要不就是扯淡，要不就是我测得有问题）。我用我手上的Pad（支持BLE central，android5.0）发现直接不支持，Android5.0 SDK已经开始支持check手机是否支持BLE Peripheral。**



检查是否支持BLE、BLE Peripheral。代码如下
```
private void init(){  
    if(!getPackageManager().hasSystemFeature(PackageManager.FEATURE_BLUETOOTH_LE)){  
        Toast.makeText(this, R.string.ble_not_supported, Toast.LENGTH_LONG).show();  
        finish();  
    }  
      
    final BluetoothManager mBluetoothManager = (BluetoothManager) getSystemService(BLUETOOTH_SERVICE);  
    mBluetoothAdapter = mBluetoothManager.getAdapter();  
      
    if(mBluetoothAdapter ==  null){  
        Toast.makeText(this, R.string.bluetooth_not_supported, Toast.LENGTH_LONG).show();  
        finish();  
    }  
      
    mBluetoothLeAdvertiser = mBluetoothAdapter.getBluetoothLeAdvertiser();  
    if(mBluetoothLeAdvertiser == null){  
        Toast.makeText(this, "the device not support peripheral", Toast.LENGTH_SHORT).show();  
        Log.e(TAG, "the device not support peripheral");  
        finish();  
    }  
}  
```

关键代码：
```
mBluetoothLeAdvertiser = mBluetoothAdapter.getBluetoothLeAdvertiser();  
```
这一句代码会直接判断你的设备到底支持不支持BLE Peripheral。假如此返回值非空，你才可以继续有机会开发，如果返回空，那说明你的设备搞不了BLE Peripheral。


支持不支持BLE Peripheral，你也可以用BluetoothAdapter类的isMultipleAdvertisementSupported()函数去check，实际上getBluetoothLeAdvetiser（）也会执行上面的isMultipleAdvertisementSupported函数



### Peripheral端
1. 设置蓝牙权限
	```
	<uses-permission android:name="android.permission.BLUETOOTH" />
	<uses-permission android :name="android.permission.BLUETOOTH_ADMIN" />
	```
2. 打开蓝牙
	```
	mBluetoothManager = (BluetoothManager) getSystemService(Context. BLUETOOTH_SERVICE);
	mBluetoothAdapter = mBluetoothManager.getAdapter();
	if (mBluetoothAdapter == null) {
	    Toast.makeText( this, "手机不支持peripheral", Toast. LENGTH_LONG).show();
	    Log. e(TAG, "Bluetooth not supported" );
	    finish() ;
	} else if (! mBluetoothAdapter.isEnabled()) {
	    // Make sure bluetooth is enabled.
	    /*Intent enableBtIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
	    startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT);*/
	    mBluetoothAdapter .enable();//强制打开蓝牙
	}
	```
3. 打开服务（使客户端能够发现该外围设备）
	3.1. 设置外围设备配置
		```
		mAdvertiseSettings =  new AdvertiseSettings.Builder()
	        .setAdvertiseMode(AdvertiseSettings.ADVERTISE_MODE_BALANCED)
	        .setTxPowerLevel(AdvertiseSettings.ADVERTISE_TX_POWER_MEDIUM)
	        .setConnectable(true )
	        .build();
		```
	3.2. 设置外围设备显示数据
		```
		mAdvData = new AdvertiseData.Builder()
	        .setIncludeDeviceName(true)
	        .setIncludeTxPowerLevel(true)
	        .addServiceUuid(new ParcelUuid(UUID_SERVICE))
	        .build();
		```
	3.3. 开启外围服务
		```
		mBluetoothLeAdvertiser = mBluetoothAdapter.getBluetoothLeAdvertiser();
		mBluetoothLeAdvertiser.startAdvertising(mAdvertiseSettings, mAdvData, mAdvertiseCallback);
		mGattServer = mBluetoothManager.openGattServer(this, mBluetoothGattServerCallback); 
		```
4. 给外围设备添加Server和Characteristic
	```
	BluetoothGattService service = new BluetoothGattService(UUID_SERVICE,BluetoothGattService. SERVICE_TYPE_PRIMARY);
	BluetoothGattCharacteristic characteristic = new BluetoothGattCharacteristic(UUID_Characteristic,BluetoothGattCharacteristic. PROPERTY_READ|BluetoothGattCharacteristic.PROPERTY_WRITE, BluetoothGattCharacteristic. PERMISSION_READ|BluetoothGattCharacteristic.PERMISSION_WRITE) ;
	service.addCharacteristic(characteristic) ;
	final boolean b = mGattServer.addService(service);
	```
5. Characteristic的值改变时通知客户端
	```
	final BluetoothGattService service = mGattServer.getService(UUID_SERVICE );
	final BluetoothGattCharacteristic characteristic = service.getCharacteristic( UUID_Characteristic);
	String str = editText.getText().toString();
	Log.i(TAG, str.length()+"");
	characteristic.setValue(str);
	boolean b = mGattServer.notifyCharacteristicChanged(romoteDevice, characteristic,true) ;
	```
6. 实现BluetoothGattServerCallback
	6.1. 在客户端readCharacteristic时会触发 `onCharacteristicReadRequest` 事件，调用
	```
	mGattServer.sendResponse(device, requestId, BluetoothGatt.GATT_SUCCESS,offset, characteristic.getValue());
	``` 
	来通知客户端的回调
	6.2. 在客户端writeCharacteristic时会触发 `onCharacteristicWriteRequest` 事件，调用
	```
	mGattServer .sendResponse
	``` 
	来通知客户端回调

7. 关闭peripheral
	```
	mBluetoothLeAdvertiser.stopAdvertising(mAdvertiseCallback);
	```


### BLE客户端
1. 设置权限和打开蓝牙和peripheral端一样设置
2. 搜索BLE设备
	```
	mAdapter .startLeScan(leScanCallback) ;
	LeScanCallback leScanCallback = new LeScanCallback() {
            @Override
            public void onLeScan (final BluetoothDevice bluetoothdevice , int i, byte [] abyte0) {
                runOnUiThread( new Runnable() {
                     @Override
                     public void run() {
                        findDevice( bluetoothdevice);
                     }
                }) ;
            }
	};
	```
3. 关闭搜索
	```
	mAdapter.stopLeScan(leScanCallback);
	```
4. 连接BLE设备
	```
	BluetoothGatt connectGatt = remoteDevice.connectGatt(this, false, mBluetoothGattCallback);
	```
5. 向peripheral端写入数据
	```
	final BluetoothGattService service = connectGatt.getService(UUID_SERVICE );
	final BluetoothGattCharacteristic characteristic = service.getCharacteristic( UUID_Characteristic);
	int property = characteristic.getProperties() ;
	Log.e(TAG, "---->char property:" +Utils.getCharPropertie(property));
	connectGatt .setCharacteristicNotification(characteristic , true);
	
	String str = editText.getText().toString();
	Log. i(TAG, str.length()+"") ;
	characteristic.setValue(str) ;
	
	boolean b = connectGatt.writeCharacteristic(characteristic);
	```
6. 读取peripheral端的数据
	```
	final BluetoothGattService service = connectGatt.getService(UUID_SERVICE);
	final BluetoothGattCharacteristic characteristic = service.getCharacteristic( UUID_Characteristic);
	byte[] value = characteristic.getValue();
	
	boolean b= connectGatt.readCharacteristic(characteristic);
	```

学习资料：https://github.com/WebBluetoothCG/ble-test-peripheral-android
https://github.com/suzp1984/Light_BLE
https://github.com/Sensirion/libble-android

注：peripheral只能20个字节
client超过20个字节就只能发送18个字节