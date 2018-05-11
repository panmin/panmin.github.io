--
layout:     post                            # 使用的布局（不需要改）
title:      android音量控制setVolumeControlStream              # 标题
subtitle:      #副标题
date:       2017-04-21                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


当开发多媒体应用或者游戏应用的时候，需要使用音量控制键来设置程序的音量大小。在Android系统中有多中音频流，通过Activity中的函数 setVolumeControlStream(int streamType)可以设置该Activity中音量控制键控制的音频流，一般在onCreate函数中设置。Android中有如下几种音频流（streamType是需要调整音量的类型）：

AudioManager.STREAM_MUSIC /音乐回放即媒体音量/

AudioManager.STREAM_RING /铃声/

AudioManager.STREAM_ALARM /警报/

AudioManager.STREAM_NOTIFICATION /窗口顶部状态栏通知声/

AudioManager.STREAM_SYSTEM /系统/

AudioManager.STREAM_VOICECALL /通话 /

AudioManager.STREAM_DTMF /双音多频,不是很明白什么东西 /

AudioManager可以修改系统Android系统的音量,下面介绍几个AudioManager的几个音量调整方面的方法.首先是得到AudioManager实例:

Java代码

AudioManager am=(AudioManager)getSystemService(Context.AUDIO_SERVICE);

调整音量方法有两种,一种是渐进式,即像手动按音量键一样,一步一步增加或减少,另一种是直接设置音量值.1、渐进式

Java代码

publicvoidadjustStreamVolume (intstreamType, intdirection, intflags)

 

am.adjustStreamVolume (AudioManager.STREAM_MUSIC, AudioManager.ADJUST_RAISE, AudioManager.FLAG_SHOW_UI);

解释一下三个参数

Java代码

第一个streamType是需要调整音量的类型,这里设的是媒体音量,可以是:

STREAM_ALARM 警报

STREAM_MUSIC 音乐回放即媒体音量

STREAM_NOTIFICATION 窗口顶部状态栏Notification,

STREAM_RING 铃声

STREAM_SYSTEM 系统

STREAM_VOICE_CALL 通话

STREAM_DTMF 双音多频,不是很明白什么东西

 

第二个direction,是调整的方向,增加或减少,可以是:

ADJUST_LOWER 降低音量

ADJUST_RAISE 升高音量

ADJUST_SAME 保持不变,这个主要用于向用户展示当前的音量

 

第三个flags是一些附加参数,只介绍两个常用的

FLAG_PLAY_SOUND 调整音量时播放声音

FLAG_SHOW_UI 调整时显示音量条,就是按音量键出现的那个

0表示什么也没有

2、直接设置音量值的方法:

Java代码

publicvoidsetStreamVolume (intstreamType, intindex, intflags)

 

am.setStreamVolume(AudioManager.STREAM_MUSIC, am.getStreamMaxVolume(AudioManager.STREAM_MUSIC), AudioManager.FLAG_PLAY_SOUND);

am.getStreamMaxVolume(AudioManager.STREAM_VOICE_CALL);//得到听筒模式的最大值

am.getStreamVolume(AudioManager.STREAM_VOICE_CALL);//得到听筒模式的当前值

第一个和第三个参数与上面的相同第二个参数是一个音量的int值,getStreamMaxVolume(int streamType)得到的是该类型音量的最大值,可以根据这个值计算你需要的音量,我这里直接调到最大.