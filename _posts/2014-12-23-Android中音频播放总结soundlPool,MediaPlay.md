---
layout:     post                            # 使用的布局（不需要改）
title:      Android中音频播放总结soundlPool,MediaPlay                  # 标题
subtitle:           #副标题
date:       2014-12-23                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android
---


## Android中音频播放总结soundlPool,MediaPlay

### soundlPool 
用于小音频的播放多个同时播放。
使用步骤：
1. 首先下载音频文件可以将其放入assets文件夹下或者res下的raw文件夹下，区别在于assets下可以再新建文件夹二raw不行，assets内部单个文件超过1m时可能存在bug而raw不会。soundpool的音频文件大小不能超过1M同时时间超过5-6秒可能会出错。
2. 构造实例
	SoundPool(int maxStreams, int streamType, int srcQuality) 参数依次是：
	* 指定支持多少个声音，SoundPool对象中允许同时存在的最大流的数量，该值太大就会报错AudioFlinger could not  create track, status: -12 ，就听不到声音
	* 指定声音类型，流类型可以分为STREAM_VOICE_CALL, STREAM_SYSTEM, STREAM_RING,STREAM_MUSIC 和STREAM_ALARM四种类型。在AudioManager中定义。
	* 指定声音品质（采样率变换质量），一般直接设置为0！
	在低版本中可以用上述构造方法，而API 21(Android 5.0)后这个构造方法就过时了！ 而用到一个SoundPool.Builder的东东，我们要实例化SoundPool只需调用：
	```
	SoundPool.Builder spb = new SoundPool.Builder();
	spb.setMaxStreams(10);
	spb.setAudioAttributes(null);    //转换音频格式
	SoundPool sp = spb.build();      //创建SoundPool对象
	```
	要使用上述代码的话，TargetSDK版本要设置大于等于21。

3. 加载声音资源文件
	* load(Context context, int resId, int priority) //从APK资源载入
	* load(String path, int priority)
	* load(FileDescriptor fd, long offset, long length, int priority)
	* load(AssetFileDescriptor afd, int priority)

	参数介绍：
	**context**：上下文
	**resId**：资源id，如葱raw文件获取填写R.raw.xxx
	**priority**：没什么用的一个参数，建议设置为1，保持和未来的兼容性
	**path**：文件路径,文件的绝对路线，如存放在sd卡中的音频
	**FileDescriptor**：貌似是流吧，这个我也不知道  http://www.fengfly.com/plus/view-214059-1.html
	**AssetFileDescriptor**：从asset目录读取某个资源文件，context.getAssets().openFd("xxx"),xxx表示文件名

	上述方法都会返回一个声音的ID，Integer类型，我们可以通过建立一个Map<Integer,Integer> 来存储和获取声音方法如下，
	```
	Map<Integer,Integer> map=new HashMap<Integer, Integer>();
	map.put(1,soundPool.load(context.getAssets().openFd("FadeOut.ogg"),1));
	```
	当调用load方法的时候实际就是把音效加载到了 SoundPool中，此时返回的streamId其实就是该音效在SoundPool中的Id，这个ID从0还是1来着（有点记不清了）递增，不过要注意的是，不要超过  256  这个临界点。也就是说第257个声音加载进去后，调用play方法其实是播不出来的，说不定还会挤掉一些前面加载好的声音。这个256的限制通过查看SDK源码基本就能了解清楚，它底层就那么实现的，用一个类似堆栈来存。
4. 播放音频文件

	**play(int soundID, float leftVolume, float rightVolume, int priority, int loop, float rate)**,其返回值为一个int类型的数字

	参数依次是：
	**soundID**：Load()返回的声音ID号，以上可以通过map.get(1)获取
	**leftVolume**：左声道音量设置  一般为0－1，默认填1
	**rightVolume**：右声道音量设置 一般为0－1，默认填1
	**priority**：指定播放声音的优先级，数值越高，优先级越大。默认填0
	**loop**：指定是否循环：-1表示无限循环，0表示不循环，其他值表示要重复播放的次数
	**rate**：指定播放速率：1.0的播放率可以使声音按照其原始频率，而2.0的播放速率，可以使声音按照其 原始频率的两倍播放。如果为0.5的播放率，则播放速率是原始频率的一半。播放速率的取值范围是0.5至2.0。

	如果SoundPool刚调完加载load函数之后，直接调用SoundPool的play函数可能出现error "sample 1 not READY",所以建议，调用加载资源函数load之后，实现资源加载结束的监听函数，在这个监听到资源加载结束之后，播放音频文件。

	```
	soundPool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
        @Override
        public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
            soundPool.play(map.get(1),1,1,0,0,1);
        }
    });
	```
5. 去除音频或者停止播放重置资源
	**soundPool.pause(int streamID)**  暂停指定播放流的音效
		streamID：应通过play()返回
	**soundPool.resume(int streamID)**  继续播放指定播放流的音效
		streamID：应通过play()返回
	**soundPool.stop(int streamID)** 终止指定播放流的音效
		streamID：应通过play()返回
	**soundPool.unload(int soundID)** 卸载一个指定的音频资源.
		soundID：Load()返回的声音ID号，以上可以通过map.get(1)获取
 	**soundPool.release();** 释放SoundPool中的所有音频资源.
	
	注意：
	1. play()函数传递的是一个load()返回的soundID——指向一个被记载的音频资源 ，如果播放成功则返回一个非0的streamID——指向一个成功播放的流 ；同一个soundID 可以通过多次调用play()而获得多个不同的streamID (只要不超出同时播放的最大数量)；
	2. pause()、resume()和stop()是针对播放流操作的，传递的是play()返回的streamID ;
	3. play()中的priority参数，只在同时播放的流的数量超过了预先设定的最大数量是起作用，管理器将自动终止优先级低的播放流。如果存在多个同样优先级的流，再进一步根据其创建事件来处理，新创建的流的年龄是最小的，将被终止；
	4. 无论如何，程序退出时，手动终止播放并释放资源是必要的。
	5. 如果你音效多，也不要指望unload方法来清除掉一些音效后再load新的进去，虽然unload后音效卸载了，但是前面分给它在SoundPool里面的Id可没有释放掉，也就是说这个时候你load新的进去只会在后面继续累加，然后累加多了就超过256了，然后就就听不到声音，然后就没有然后了。要想彻底清掉前面的音效请使用release方法，它会连内存中占用的资源一起释放掉。
	6. 其他还有点什么呢，load需要一点点时间，load后不要马上unload，load ---play--unload的做法并不可取，不要load太大的音效，它只会申请1M的内存空间。SoundPool出错后通常会看到retuen的值是0。


### mediaPlay播放音频文件
使用步骤：
1. 新建对象
	```
	MediaPlayer mp = new MediaPlayer();//使用这种方式获取对象是因为你所播放的音频文件在sd卡或者是网络音频等，随后需要添加(setDataSource)自己需要播放的音频，使用这种方式新建的对象需要将播放器进入prepare状态，下面会说。
	MediaPlayer mp = MediaPlayer.create(this, R.raw.music);//使用这种方式直接将工程中的音频播放，使用这种方式不用讲播放器进入prepare状态，因为在creat时已经自己做了这步操作。
	```
2. 添加需要播放的音频文件
	mediaplay播放的音频主要来自于4个来源
	1. res文件夹下的raw文件，添加方式如下
		```
　　		MediaPlayer.create(this, R.raw.test);
		```
	2. src/main下的assets文件，添加方式如下
		```
　　		AssetFileDescriptor fileDescriptor = getAssets().openFd("music.mp3");
　　		mediaPlayer.setDataSource(fileDescriptor.getFileDescriptor(),fileDescriptor.getStartOffset(), fileDescriptor.getLength());
		```
	3. 网络上的音频文件
		```
　　		mp.setDataSource("http://www.xxxxxxx/music.mp3");
		```
	4. 本地sd卡下的文件
		```
　　		mp.setDataSource("/sdcard/music.mp3");
		```
3. 控制播放器进入prepare状态
	> prepare()和prepareAsync() 提供了同步和异步两种方式设置播放器进入prepare状态，需要注意的是，如果MediaPlayer实例是由create方法创建的，那么第一次启动播放前不需要再调用prepare（）了，因为create方法里已经调用过了。
	
	start()、pause()和stop()比较简单，起到暂停和停止播放的作用

	**seekTo()**是定位方法，可以让播放器从指定的位置开始播放，需要注意的是该方法是个异步方法，也就是说该方法返回时并不意味着定位完成，尤其是播放的网络文件，真正定位完成时会触发OnSeekComplete.onSeekComplete()，如果需要是可以调用setOnSeekCompleteListener(OnSeekCompleteListener)设置监听器来处理的。

	**release()**可以释放播放器占用的资源，一旦确定不再使用播放器时应当尽早调用它释放资源。

　	**reset()**可以使播放器从Error状态中恢复过来，重新会到Idle状态。

　	**setLooping()**设置循环播放

　	**setOnCompletionListener(MediaPlayer.OnCompletionListener listener)**播放完成监听。

　	**setOnErrorListener(MediaPlayer.OnErrorListener listener)**播放错误监听