---
layout:     post                            # 使用的布局（不需要改）
title:      Android经典蓝牙socket通信                  # 标题
subtitle:           #副标题
date:       2015-06-23                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android经典蓝牙socket通信
1. server和client通用代码
	```
	BluetoothAdapter mAdapter = BluetoothAdapter.getDefaultAdapter();//获取蓝牙适配器
	mAdapter .enable();// 强制打开蓝牙,不打开提示框
	```
2. 注册广播接收者
	```
	IntentFilter filter = new IntentFilter();
	filter.addAction(BluetoothDevice. ACTION_FOUND);//蓝牙设备被找到
	filter.addAction(BluetoothDevice. ACTION_BOND_STATE_CHANGED);//蓝牙设备的配对状态改变
	registerReceiver( receiver, filter);//注册
	BroadcastReceiver receiver = new BroadcastReceiver() {
           @Override
           public void onReceive(Context context, Intent intent) {
                 String action = intent.getAction();
                  if (BluetoothDevice.ACTION_FOUND.equals(action)) {// 找到设备
                       BluetoothDevice dev = intent.getParcelableExtra(BluetoothDevice. EXTRA_DEVICE);
                       //TODO 将获取的设备信息添加到设备列表中
                 } else if (BluetoothDevice.ACTION_BOND_STATE_CHANGED .equals(action)) {// 设备状态改变
                        device = intent.getParcelableExtra(BluetoothDevice. EXTRA_DEVICE);
                        switch (device .getBondState()) {
                        case BluetoothDevice.BOND_BONDING:
                              Log. d("BlueToothTestActivity", "正在配对......" );
                              Toast. makeText(MainActivity.this, "正在配对......" , Toast.LENGTH_SHORT).show();
                               break;
                        case BluetoothDevice.BOND_BONDED:
                              Log. d("BlueToothTestActivity", "完成配对" );
                              Toast. makeText(MainActivity.this, "完成配对" , Toast.LENGTH_SHORT).show();
                               tv_state.setText( "已配对");
                               tv_bt.setText( device.getName()+ "--"+device .getAddress());
                              acceptThread();
                               break;
                        case BluetoothDevice.BOND_NONE:
                              Log. d("BlueToothTestActivity", "取消配对" );
                              Toast. makeText(MainActivity.this, "取消配对......" , Toast.LENGTH_SHORT).show();
                               tv_state.setText( "配对失败" );
                        default:
                               break;
                       }
                 } 
          }
    };
	```
3. 开始蓝牙设备搜索
	```
	mAdapter.startDiscovery();
	```
4. 关闭蓝牙搜索
	```
	mAdapter.cancelDiscovery();
	```
5. 配对
	```
	private void pair(){
       if (device .getBondState() == BluetoothDevice.BOND_NONE) {//未配过对的
          try {
                boolean b1 = ClsUtils. setPin(device.getClass(), device,
                              strPsw);
                boolean b2 = ClsUtils.createBond( device.getClass(),
                              device);
               //ClsUtils.cancelBondProcess(device.getClass(), device);
               ClsUtils. cancelPairingUserInput(device.getClass(), device);
               System. out.println(b2);
         } catch (Exception e) {
                // TODO Auto-generated catch block
               e.printStackTrace();
         }

      } else if (device .getBondState() == BluetoothDevice.BOND_BONDED){//配过对的先解除配对，再配对
          try {
                boolean b1 = ClsUtils. removeBond(device.getClass(),
                              device);
              // boolean b1 = ClsUtils.createBond(device.getClass(),device);
                boolean b2 = ClsUtils. setPin(device.getClass(), device,
                              strPsw); // 手机和蓝牙采集器配对
                boolean b3 = ClsUtils.createBond( device.getClass(),
                              device);
         //ClsUtils.cancelBondProcess(device.getClass(), device);
                boolean b4 =ClsUtils.cancelPairingUserInput(device.getClass(), device);
               System. out.println(b3);
         } catch (Exception e) {
                // TODO Auto-generated catch block
               e.printStackTrace();
         }
      }
    }
	```
6. 配对核心类
	```
	public class ClsUtils {
        /**
        * 与设备配对 参考源码：platform/packages/ apps/Settings.git
        * /Settings/ src/com /android/settings/bluetooth/CachedBluetoothDevice.java
        */
        static public boolean createBond(Class btClass, BluetoothDevice btDevice)
                      throws Exception {
              Method createBondMethod = btClass.getMethod("createBond");
              Boolean returnValue = (Boolean) createBondMethod.invoke(btDevice);
               return returnValue.booleanValue();
       }
        // 自动配对设置Pin值
        static public boolean autoBond(Class btClass, BluetoothDevice device, String strPin)
               throws Exception {
           Method autoBondMethod = btClass.getMethod("setPin", new Class[] { byte[].class });
           Boolean result = (Boolean) autoBondMethod
                   .invoke(device, new Object[] { strPin.getBytes() });
           return result;
       }
        /**
        * 与设备解除配对 参考源码：platform/packages/ apps/Settings.git
        * /Settings/ src/com /android/settings/bluetooth/CachedBluetoothDevice.java
        */
        static public boolean removeBond(Class btClass, BluetoothDevice btDevice)
                      throws Exception {
              Method removeBondMethod = btClass.getMethod("removeBond");
              Boolean returnValue = (Boolean) removeBondMethod.invoke(btDevice);
               return returnValue.booleanValue();
       }

        static public boolean setPin(Class btClass, BluetoothDevice btDevice,
                     String str) throws Exception {
               try {
                     Method removeBondMethod = btClass.getDeclaredMethod("setPin",
                                   new Class[] { byte[].class }) ;
                     Boolean returnValue = (Boolean) removeBondMethod.invoke(btDevice,
                                   new Object[] { str.getBytes() });
                     Log. e("returnValue", "" + returnValue);
              } catch (SecurityException e) {
                      // throw new RuntimeException(e.getMessage());
                     e.printStackTrace();
              } catch (IllegalArgumentException e) {
                      // throw new RuntimeException(e.getMessage());
                     e.printStackTrace();
              } catch (Exception e) {
                      // TODO Auto-generated catch block
                     e.printStackTrace();
              }
               return true ;

       }

        // 取消用户输入
        static public boolean cancelPairingUserInput(Class btClass,
                     BluetoothDevice device)

        throws Exception {
              Method createBondMethod = btClass.getMethod("cancelPairingUserInput") ;
               // cancelBondProcess()
              Boolean returnValue = (Boolean) createBondMethod.invoke(device);
               return returnValue.booleanValue();
       }

        // 取消配对
        static public boolean cancelBondProcess(Class btClass,
                     BluetoothDevice device)

        throws Exception {
              Method createBondMethod = btClass.getMethod("cancelBondProcess");
              Boolean returnValue = (Boolean) createBondMethod.invoke(device);
               return returnValue.booleanValue();
       }

        /**
        * @param clsShow
        */
        static public void printAllInform(Class clsShow) {
               try {
                      // 取得所有方法
                     Method[] hideMethod = clsShow.getMethods();
                      int i = 0;
                      for (; i < hideMethod.length ; i++) {
                           Log. e("method name", hideMethod[i].getName() + ";and the i is:"
                                         + i);
                     }
                      // 取得所有常量
                     Field[] allFields = clsShow.getFields();
                      for (i = 0; i < allFields.length ; i++) {
                           Log. e("Field name", allFields[i].getName());
                     }
              } catch (SecurityException e) {
                      // throw new RuntimeException(e.getMessage());
                     e.printStackTrace();
              } catch (IllegalArgumentException e) {
                      // throw new RuntimeException(e.getMessage());
                     e.printStackTrace();
              } catch (Exception e) {
                      // TODO Auto-generated catch block
                     e.printStackTrace();
              }
       }
	}
	```