--
layout:     post                            # 使用的布局（不需要改）
title:      Android Bluetooth Low Energy 基础             # 标题
subtitle:      #副标题
date:       2017-01-11                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android ble
---



1、关于Android平台的BLE

蓝牙4.0于2010年发布，相对于上个版本3.0，它的特点是更省电、成本低 延迟低等特点，现在最新的蓝牙协议是2013年底发布的蓝牙4.1，蓝牙4.1在4.0 基础上进行升级，使得可穿戴设备的批量数据传输速度更高。Android是从4.3 才开始提供BLE API,这也就限定了BLE的应用只能运行在Android 4.3及其以上 的系统。在Android平台上的蓝牙4.0主要有两种工作模式:经典蓝牙(classic bluetooth) 、低功耗蓝牙(bluetooth low energy,缩写为BLE)

2、角色与职责

当一个Android设备与一个BLE设备进行交互通信时，主要存在以下两种关系
中心设备与外围设备：中心设备扮演扫描的角色，寻找外围设备的广播消息。Android设备 作为中心设备，与之连接通信的设备作为外围设备。

GATT服务器与GATT客户端：这种关系决定了当连接建立后两个设备如何通信。

注：目前Android系统提供的API使得Android设备只能作为中心设备

3、组成部分

BLE分为三个部分Service、Characteristic、Descriptor，每个部分都拥有不同的 UUID来标识。一个BLE设备可以拥有多个Service，一个Service可以包含多个Characteristic， 一个Characteristic包含一个Value和多个Descriptor，一个Descriptor包含一个Value。 通信数据一般存储在Characteristic内，目前一个Characteristic中存储的数据最大为20 byte。 与Characteristic相关的权限字段主要有READ、WRITE、WRITE_NO_RESPONSE、NOTIFY。 Characteristic具有的权限属性可以有一个或者多个。

4、核心代码
```
private BluetoothAdapter mBtAdapter = null;
private BluetoothGatt mBtGatt = null;
private int mState = 0;
private Context mContext;
private BluetoothGattCharacteristic mWriteCharacteristic = null;
private BluetoothGattCharacteristic mReadCharacteristric = null;

private final String TAG = "BLE_Demo";

// 设备连接状态
private final int CONNECTED = 0x01;
private final int DISCONNECTED = 0x02;
private final int CONNECTTING = 0x03;

// 读写相关的Service、Characteristic的UUID
public static final UUID TRANSFER_SERVICE_READ = UUID.fromString("34567817-2432-5678-1235-3c1d5ab44e17");
public static final UUID TRANSFER_SERVICE_WRITE = UUID.fromString("34567817-2432-5678-1235-3c1d5ab44e18");
public static final UUID TRANSFER_CHARACTERISTIC_READ = UUID.fromString("23487654-5678-1235-2432-3c1d5ab44e94");
public static final UUID TRANSFER_CHARACTERISTIC_WRITE = UUID.fromString("23487654-5678-1235-2432-3c1d5ab44e93");

// BLE设备连接通信过程中回调
private BluetoothGattCallback mBtGattCallback = new BluetoothGattCallback() {

    // 连接状态发生改变时的回调
    @Override
    public void onConnectionStateChange(BluetoothGatt gatt, int status,
                int newState) {

        if (status == BluetoothGatt.GATT_SUCCESS) {
            mState = CONNECTED;
            Log.d(TAG, "connected OK");
            mBtGatt.discoverServices();
        } else if (newState == BluetoothGatt.GATT_FAILURE) {
            mState = DISCONNECTED;
            Log.d(TAG, "connect failed");
        }
    }

    // 远端设备中的服务可用时的回调
    @Override
    public void onServicesDiscovered(BluetoothGatt gatt, int status) {

        if (status == BluetoothGatt.GATT_SUCCESS) {
            BluetoothGattService btGattWriteService = mBtGatt
                    .getService(TRANSFER_SERVICE_WRITE);
            BluetoothGattService btGattReadService = mBtGatt
                    .getService(TRANSFER_SERVICE_READ);
            if (btGattWriteService != null) {
                mWriteCharacteristic = btGattWriteService
                        .getCharacteristic(TRANSFER_CHARACTERISTIC_WRITE);
            }
            if (btGattReadService != null) {
                mReadCharacteristric = btGattReadService
                        .getCharacteristic(TRANSFER_CHARACTERISTIC_READ);
                if (mReadCharacteristric != null) {
                    mBtGatt.readCharacteristic(mReadCharacteristric);
                }
            }
        }
    }

    // 某Characteristic的状态为可读时的回调
    @Override
    public void onCharacteristicRead(BluetoothGatt gatt,
            BluetoothGattCharacteristic characteristic, int status) {

        if (status == BluetoothGatt.GATT_SUCCESS) {
            readCharacterisricValue(characteristic);

            // 订阅远端设备的characteristic，
            // 当此characteristic发生改变时当回调mBtGattCallback中的onCharacteristicChanged方法
            mBtGatt.setCharacteristicNotification(mReadCharacteristric,
                    true);
            BluetoothGattDescriptor descriptor = mReadCharacteristric
                    .getDescriptor(UUID
                            .fromString("00002902-0000-1000-8000-00805f9b34fb"));
            if (descriptor != null) {
                byte[] val = BluetoothGattDescriptor.ENABLE_NOTIFICATION_VALUE;
                descriptor.setValue(val);
                mBtGatt.writeDescriptor(descriptor);
            }
        }
    }

    // 写入Characteristic成功与否的回调
    @Override
    public void onCharacteristicWrite(BluetoothGatt gatt,
            BluetoothGattCharacteristic characteristic, int status) {

        switch (status) {
        case BluetoothGatt.GATT_SUCCESS:
            Log.d(TAG, "write data success");
            break;// 写入成功
        case BluetoothGatt.GATT_FAILURE:
            Log.d(TAG, "write data failed");
            break;// 写入失败
        case BluetoothGatt.GATT_WRITE_NOT_PERMITTED:
            Log.d(TAG, "write not permitted");
            break;// 没有写入的权限
        }
    }

    // 订阅了远端设备的Characteristic信息后，
    // 当远端设备的Characteristic信息发生改变后,回调此方法
    @Override
    public void onCharacteristicChanged(BluetoothGatt gatt,
            BluetoothGattCharacteristic characteristic) {
        readCharacterisricValue(characteristic);
    }

};

/**
 * 读取BluetoothGattCharacteristic中的数据
 * 
 * @param characteristic
 */
private void readCharacterisricValue(
        BluetoothGattCharacteristic characteristic) {
    byte[] data = characteristic.getValue();
    StringBuffer buffer = new StringBuffer("0x");
    int i;
    for (byte b : data) {
        i = b & 0xff;
        buffer.append(Integer.toHexString(i));
    }
    Log.d(TAG, "read data:" + buffer.toString());
}

/**
 * 与指定的设备建立连接
 * 
 * @param device
 */
public void connect(BluetoothDevice device) {

    mBtGatt = device.connectGatt(mContext, false, mBtGattCallback);
    mState = CONNECTTING;
}

/**
 * 初始化
 * 
 * @param context
 * @return 如果初始化成功则返回true
 */
public boolean init(Context context) {
    BluetoothManager btMrg = (BluetoothManager) context
            .getSystemService(Context.BLUETOOTH_SERVICE);
    if (btMrg == null)
        return false;
    mBtAdapter = btMrg.getAdapter();
    if (mBtAdapter == null)
        return false;
    mContext = context;
    return true;
}

// BLE设备搜索过程中的回调，在此可以根据外围设备广播的消息来对设备进行过滤
private BluetoothAdapter.LeScanCallback mLeScanCallback = new BluetoothAdapter.LeScanCallback() {

    @Override
    public void onLeScan(final BluetoothDevice device, int rssi,
            byte[] scanRecord) {

        ArrayUtils.reverse(scanRecord);// 数组反转
        // 将Byte数组的数据以十六进制表示并拼接成字符串
        StringBuffer str = new StringBuffer();
        int i = 0;
        for (byte b : scanRecord) {
            i = (b & 0xff);
            str.append(Integer.toHexString(i));
        }
        String discoveryServceID = str.toString();
        Log.d(TAG, device.getName() + " scanRecord:\n" + discoveryServceID);

        // 查询是否含有指定的Service UUID信息
        if (discoveryServceID.indexOf(TRANSFER_SERVICE_WRITE.toString()
                .replace("-", "")) != -1) {

            Log.d(TAG, device.getName() + " has available service UUID");

            // 在这是处理匹配的设备……

        }

    }

};

/**
 * 开始BLE设备扫描
 */
public void startScan() {
    mBtAdapter.startLeScan(mLeScanCallback);
}

/**
 * 停止BLE设备扫描
 */
public void stopScan() {
    mBtAdapter.stopLeScan(mLeScanCallback);
}

/**
 * 发送数据
 * 
 * @param data
 *            待发送的数据,最大长度为20
 */
private void sendData(byte[] data) {

    if (data != null && data.length > 0 && data.length < 21) {
        if (mWriteCharacteristic.setValue(data)
                && mBtGatt.writeCharacteristic(mWriteCharacteristic)) {
            Log.d(TAG, "send data OK");
        }
    }
}
```



## Android Bluetooth Low Energy 数据的写入与读取
1 、启动ble设备连接的方法首先要得到一个BluetoothDevice， 这个可以通过，android搜索一配对或者为配对的蓝牙设备获取得到BluetoothDevice的对象，也可以通过搜索ble设备去得到一个BluetoothDevice的对象（下面会仔细说索搜ble设备）需要注意的是，当一个ble设备作为一个蓝牙设备连接到了android 设备上的话，我们通过搜索ble设备的api是搜索不到的。

2、得到一个蓝牙已配对设备
```
mBluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
mBluetoothAdapter.startDiscovery();

Set<BluetoothDevice> bledevices = mBluetoothAdapter.getBondedDevices();
if (bledevices.size() > 0)
{
    for (BluetoothDevice bluetoothDevice : bledevices)
    {
        if (bluetoothDevice.getAddress().toString()
                .equals(BLUETOOTH_DEVICE_MAC))
        {
            Log.v(TAG, "find with old: " + bluetoothDevice.getAddress());
            bluetoothDevice.connectGatt(getActivity(), false,
                    mGattCallback);
        }
    }
}
```

3、如何索搜ble设备 ：
```
使用BluetoothAdapter 的 startLeScan 方法，
```
startLeScan 的参数是一个回调。
```
private BluetoothAdapter.LeScanCallback mLeScanCallback = new BluetoothAdapter.LeScanCallback()
{

    @Override
    public void onLeScan(final BluetoothDevice device, int rssi,
            byte[] scanRecord)
    {
        Log.v(TAG, "device: " + device);   
    }
};
```
在这个回调里可以索搜到BLE设备

4、使用索搜到的设备连接
当我们搜索到设备的时候， 可以使用
```
device.connectGatt(getActivity(), false, mGattCallback);
```

进行连接，其中 mGattCallback 也是个回调方法
```
private final BluetoothGattCallback mGattCallback = new BluetoothGattCallback()
    {

        // 设备发生读操作时的回调
        @Override
        public void onCharacteristicRead(BluetoothGatt gatt,
                BluetoothGattCharacteristic characteristic, int status)
        {
            Log.v(TAG, "onCharacteristicRead: " + characteristic.getUuid());
        }

        // 设备发生写操作是的回调
        @Override
        public void onCharacteristicWrite(BluetoothGatt gatt,
                BluetoothGattCharacteristic characteristic, int status)
        {
            if (BluetoothGatt.GATT_SUCCESS == status)
            {
                Log.v(TAG, "onCharacteristicWrite: " + characteristic.getUuid());
            }
            super.onCharacteristicWrite(gatt, characteristic, status);
        }

        // 设备连接状态发生更改时的回调
        // 我们要在这里进行discoverServices()
        @Override
        public void onConnectionStateChange(BluetoothGatt gatt, int status,
                int newState)
        {
            gatt.discoverServices();
            super.onConnectionStateChange(gatt, status, newState);
        }

        // 当BLE设备中的服务被找到时，回调如下方法
        @Override
        public void onServicesDiscovered(BluetoothGatt gatt, int status)
        {
            Log.v(TAG, "onServicesDiscovered: " + gatt.getServices().size());

            super.onServicesDiscovered(gatt, status);
        }

    };
```

5、重点：数据的IO
无论数据或是写，都需要
```
BluetoothGattService mSVC = gatt.getService(UUID
                    .fromString("0000dfb0-0000-1000-8000-00805f9b34fb"));
BluetoothGattCharacteristic mCH = mSVC.getCharacteristic(UUID
                    .fromString("0000dfb1-0000-1000-8000-00805f9b34fb"));
```

得到一个BluetoothGattCharacteristic ， 如上， 我们同过一个指定uuid的方式，在

public void onServicesDiscovered() 方法中得到gatt ，调用gatt的读操作
```
gatt.readCharacteristic(mCH);
```
执行这部操作之后， 我们会在 onCharacteristicRead 方法回掉这个 Characteristic 的所带有的value

写操作则是
```
mCH.setValue(GAME_MODE);
gatt.writeCharacteristic(mCH);
```

同理，在 onCharacteristicWrite 方法中可以获得操作的提示。
Characteristic 里的数据，结果也正确， 但是无法语言写入是否正确，目前执行了write的操作，系统显示写入正常， 但是看不到相应的变化


写数据的时候将值这样写 ch.setValue(new byte[] { (byte) 0x00, (byte) 0xff });
```
import java.util.Set;
import java.util.UUID;

import youten.redo.ble.util.BleUtil;
import youten.redo.ble.util.BleUuid;
import android.app.Activity;
import android.bluetooth.BluetoothAdapter;
import android.bluetooth.BluetoothDevice;
import android.bluetooth.BluetoothGatt;
import android.bluetooth.BluetoothGattCallback;
import android.bluetooth.BluetoothGattCharacteristic;
import android.bluetooth.BluetoothGattService;
import android.bluetooth.BluetoothManager;
import android.bluetooth.BluetoothProfile;
import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.Window;
import android.widget.Button;
import android.widget.Toast;

public class DeviceActivity extends Activity implements View.OnClickListener
{
    private static final String TAG = "BLEDevice";

    public static final String EXTRA_BLUETOOTH_DEVICE = "BT_DEVICE";
    private BluetoothAdapter mBTAdapter;
    private BluetoothDevice mDevice;
    private BluetoothGatt mConnGatt;
    private int mStatus;

    private Button mReadManufacturerNameButton;
    private Button mReadSerialNumberButton;
    private Button mWriteAlertLevelButton;

    private final BluetoothGattCallback mGattcallback = new BluetoothGattCallback()
    {
        @Override
        public void onConnectionStateChange(BluetoothGatt gatt, int status,
                int newState)
        {
            if (newState == BluetoothProfile.STATE_CONNECTED)
            {
                mStatus = newState;
                Log.v(TAG, "connected and discoverServices");
                mConnGatt.discoverServices();
            } else if (newState == BluetoothProfile.STATE_DISCONNECTED)
            {
                mStatus = newState;
                runOnUiThread(new Runnable()
                {
                    public void run()
                    {
                        mReadManufacturerNameButton.setEnabled(false);
                        mReadSerialNumberButton.setEnabled(false);
                        mWriteAlertLevelButton.setEnabled(false);
                    };
                });
            }
        };

        @Override
        public void onServicesDiscovered(BluetoothGatt gatt, int status)
        {
            Log.v(TAG, "is have services");
            for (BluetoothGattService service : gatt.getServices())
            {
                if ((service == null) || (service.getUuid() == null))
                {
                    continue;
                }
                if (BleUuid.SERVICE_DEVICE_INFORMATION.equalsIgnoreCase(service
                        .getUuid().toString()))
                {
                    mReadManufacturerNameButton
                            .setTag(service.getCharacteristic(UUID
                                    .fromString(BleUuid.CHAR_MANUFACTURER_NAME_STRING)));
                    mReadSerialNumberButton
                            .setTag(service.getCharacteristic(UUID
                                    .fromString(BleUuid.CHAR_SERIAL_NUMBEAR_STRING)));
                    runOnUiThread(new Runnable()
                    {
                        public void run()
                        {
                            mReadManufacturerNameButton.setEnabled(true);
                            mReadSerialNumberButton.setEnabled(true);
                        };
                    });
                }
                if (BleUuid.SERVICE_IMMEDIATE_ALERT.equalsIgnoreCase(service
                        .getUuid().toString()))
                {
                    runOnUiThread(new Runnable()
                    {
                        public void run()
                        {
                            mWriteAlertLevelButton.setEnabled(true);
                        };
                    });
                    mWriteAlertLevelButton.setTag(service
                            .getCharacteristic(UUID
                                    .fromString(BleUuid.CHAR_ALERT_LEVEL)));
                }
            }

            runOnUiThread(new Runnable()
            {
                public void run()
                {
                    setProgressBarIndeterminateVisibility(false);
                };
            });
        };

        @Override
        public void onCharacteristicRead(BluetoothGatt gatt,
                BluetoothGattCharacteristic characteristic, int status)
        {
            if (status == BluetoothGatt.GATT_SUCCESS)
            {

                byte[] bytes = characteristic.getValue();
                for (byte b : bytes)
                {
                    Log.v("", "------characteristic------" + b);
                }

                if (BleUuid.CHAR_MANUFACTURER_NAME_STRING
                        .equalsIgnoreCase(characteristic.getUuid().toString()))
                {
                    final String name = characteristic.getStringValue(0);

                    runOnUiThread(new Runnable()
                    {
                        public void run()
                        {
                            mReadManufacturerNameButton.setText(name);
                            setProgressBarIndeterminateVisibility(false);
                        };
                    });
                } else if (BleUuid.CHAR_SERIAL_NUMBEAR_STRING
                        .equalsIgnoreCase(characteristic.getUuid().toString()))
                {
                    final String name = characteristic.getStringValue(0);

                    runOnUiThread(new Runnable()
                    {
                        public void run()
                        {
                            mReadSerialNumberButton.setText(name);
                            setProgressBarIndeterminateVisibility(false);
                        };
                    });
                }

            }
        }

        @Override
        public void onCharacteristicWrite(BluetoothGatt gatt,
                BluetoothGattCharacteristic characteristic, int status)
        {

            if (BluetoothGatt.GATT_SUCCESS == status)
            {
                Log.v(TAG,
                        "write - characteristic: " + characteristic.getUuid());
                Log.v(TAG, "write - service "
                        + characteristic.getService().getUuid());
            }

            // 再读取一次， 验证一下值是不是对的
            gatt.readCharacteristic(characteristic);

            runOnUiThread(new Runnable()
            {
                public void run()
                {
                    setProgressBarIndeterminateVisibility(false);
                };
            });
        };
    };

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
        setContentView(R.layout.activity_device);

        // state
        mStatus = BluetoothProfile.STATE_DISCONNECTED;
        mReadManufacturerNameButton = (Button) findViewById(R.id.read_manufacturer_name_button);
        mReadManufacturerNameButton.setOnClickListener(this);
        mReadSerialNumberButton = (Button) findViewById(R.id.read_serial_number_button);
        mReadSerialNumberButton.setOnClickListener(this);
        mWriteAlertLevelButton = (Button) findViewById(R.id.write_alert_level_button);
        mWriteAlertLevelButton.setOnClickListener(this);
    }

    @Override
    protected void onResume()
    {
        super.onResume();

        init();
    }

    @Override
    protected void onDestroy()
    {
        super.onDestroy();
        if (mConnGatt != null)
        {
            if ((mStatus != BluetoothProfile.STATE_DISCONNECTING)
                    && (mStatus != BluetoothProfile.STATE_DISCONNECTED))
            {
                mConnGatt.disconnect();
            }
            mConnGatt.close();
            mConnGatt = null;
        }
    }

    @Override
    public void onClick(View v)
    {
        if (v.getId() == R.id.read_manufacturer_name_button)
        {
            if ((v.getTag() != null)
                    && (v.getTag() instanceof BluetoothGattCharacteristic))
            {
                BluetoothGattCharacteristic ch = (BluetoothGattCharacteristic) v
                        .getTag();
                if (mConnGatt.readCharacteristic(ch))
                {
                    setProgressBarIndeterminateVisibility(true);
                }
            }
        } else if (v.getId() == R.id.read_serial_number_button)
        {
            if ((v.getTag() != null)
                    && (v.getTag() instanceof BluetoothGattCharacteristic))
            {
                BluetoothGattCharacteristic ch = (BluetoothGattCharacteristic) v
                        .getTag();
                if (mConnGatt.readCharacteristic(ch))
                {
                    setProgressBarIndeterminateVisibility(true);
                }
            }

        } else if (v.getId() == R.id.write_alert_level_button)
        {
            if ((v.getTag() != null)
                    && (v.getTag() instanceof BluetoothGattCharacteristic))
            {
                BluetoothGattCharacteristic ch = (BluetoothGattCharacteristic) v
                        .getTag();
                ch.setValue(new byte[] { (byte) 0x00, (byte) 0xff });
                if (mConnGatt.writeCharacteristic(ch))
                {
                    Log.v(TAG, "1st wirte had readly");
                    setProgressBarIndeterminateVisibility(true);
                }
            }
        }
    }

    private void init()
    {
        // BLE check
        if (!BleUtil.isBLESupported(this))
        {
            Toast.makeText(this, R.string.ble_not_supported, Toast.LENGTH_SHORT)
                    .show();
            finish();
            return;
        }

        // BT check
        BluetoothManager manager = BleUtil.getManager(this);
        if (manager != null)
        {
            mBTAdapter = manager.getAdapter();
        }
        if (mBTAdapter == null)
        {
            Toast.makeText(this, R.string.bt_unavailable, Toast.LENGTH_SHORT)
                    .show();
            finish();
            return;
        }

        // check BluetoothDevice
        if (mDevice == null)
        {
            mDevice = getBTDeviceExtra();
            if (mDevice == null)
            {
                finish();
                return;
            }
        }

        // button disable
        mReadManufacturerNameButton.setEnabled(false);
        mReadSerialNumberButton.setEnabled(false);
        mWriteAlertLevelButton.setEnabled(false);

        // connect to Gatt
        if ((mConnGatt == null)
                && (mStatus == BluetoothProfile.STATE_DISCONNECTED))
        {
            Log.v(TAG, "try to connect with connectGatt");
            // try to connect
            mConnGatt = mDevice.connectGatt(this, false, mGattcallback);
            mStatus = BluetoothProfile.STATE_CONNECTING;
        } else
        {
            if (mConnGatt != null)
            {
                // re-connect and re-discover Services
                Log.v(TAG, "try to connect with connect...");
                mConnGatt.connect();
                mConnGatt.discoverServices();
            } else
            {
                Log.e(TAG, "state error");
                finish();
                return;
            }
        }
        setProgressBarIndeterminateVisibility(true);
    }

    private BluetoothAdapter mBluetoothAdapter;

    // 读取一个固定的mac 地址， 进行验证
    private static final String BLUETOOTH_DEVICE_MAC = "D0:06:8E:75:56:16";

    private BluetoothDevice getBTDeviceExtra()
    {
        BluetoothDevice bleDevice = null;
        Intent intent = getIntent();
        if (intent == null)
        {
            return null;
        }

        Bundle extras = intent.getExtras();
        if (extras == null)
        {
            return null;
        }

        mBluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
        mBluetoothAdapter.startDiscovery();

        Set<BluetoothDevice> bledevices = mBluetoothAdapter.getBondedDevices();
        if (bledevices.size() > 0)
        {
            for (BluetoothDevice bluetoothDevice : bledevices)
            {
                Log.v(TAG,
                        "bletoothdevice mac address: "
                                + bluetoothDevice.getAddress());
                if (bluetoothDevice.getAddress().toString()
                        .equals(BLUETOOTH_DEVICE_MAC))
                {
                    bleDevice = bluetoothDevice;
                }
            }
        }

        return bleDevice;
    }

}
```