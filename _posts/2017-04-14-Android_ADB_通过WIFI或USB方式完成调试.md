--
layout:     post                            # 使用的布局（不需要改）
title:      Android_ADB_通过WIFI或USB方式完成调试              # 标题
subtitle:      #副标题
date:       2017-04-14                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


#### 简介

在默认情况下adb是通过USB连接的，但是adb也支持通过wifi连接，前提是使用adb命令的电脑终端与待调试的手机在同一网段下。这样，在没有usb或者远程下都可以完成调试手机。但是手机的调试模式改为wifi后，手机连接usb就会无效，包括充电。可以将手机在wifi下切回USB即可。


#### 连接USB线转为WIFI（前提adb通过usb连接）

```
cmd：adb tcpip 5555 
//可以断开USB线，此时会发现usb充电无显示，连接数据线已无效。

//连接wifi
cmd：adb connect android设备IP地址（如：adb connect 192.168.43.144）

//断开wifi
cmd：adb disconnect
```

#### WIIF转为USB（前提adb通wifi连接）

```
cmd：adb usb
//此时USB数据线可以正常使用。
```

#### adb无连接到WIFI

```
//需要取得超级管理员权限执行su，再执行
setprop service.adb.tcp.port 5555
stop adbd
start adbd

//连接wifi
cmd：adb connect android设备IP地址（如：adb connect 192.168.43.144）
```

#### adb无连接到USB

```
//需要取得超级管理员权限执行su，再执行
setprop service.adb.tcp.port -1
stop adbd
start adbd
```


#### 脚本切换
新建一个文件命名为adbTowifi.sh
```
#!/bin/bash
   
#Modify this with your IP range
MY_IP_RANGE="192\.168\.43"

#You usually wouldn't have to modify this
PORT_BASE=5555

#List the devices on the screen for your viewing pleasure
adb devices
echo

#Find USB devices only (no emulators, genymotion or connected devices
declare -a deviceArray=(`adb devices -l | grep -v emulator | grep -v vbox | grep -v "${MY_IP_RANGE}" | grep " device " | awk '{print $1}'`)  

echo "found ${#deviceArray[@]} device(s)"
echo

for index in ${!deviceArray[*]}
do
echo "finding IP address for device ${deviceArray[index]}"
IP_ADDRESS=$(adb -s ${deviceArray[index]} shell ifconfig wlan0 | awk '{print $3}')

echo "IP address found : $IP_ADDRESS "

echo "Connecting..."
adb -s ${deviceArray[index]} tcpip $(($PORT_BASE + $index))
adb -s ${deviceArray[index]} connect "$IP_ADDRESS:$(($PORT_BASE + $index))"

echo
echo
done

adb devices -l
#exit
```

```
//以上脚本文件，mac或者Linux直接可以运行，windows上需要安装一些如msysgit或者Cygwin才可运行以上Linux shell
//前提需要usb连接adb，待执行玩命令后，可以拔掉usb数据线，此时手机切换至wifi连接，待连接上wifi后，如切回至usb，使用adb usb或者重启设备即可
sh adbTowifi.sh
```