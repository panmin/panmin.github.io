## Android原生集成Flutter

### 一、简介

**背景**：本人Android开发，目前公司的项目已经完成组件化，但是组件化带来的一个缺点就是项目编译速度变慢了，开发电脑又不给力，严重影响了开发效率；再者Android和iOS都是用原生开发的，导致同样的业务可能实现的思路不同，也增加了测试的成本；由于机制不同，两端实现的界面风格也不完全统一。

**选择flutter的原因**：编译效率高，有Hot Reload，改为代码就能看到效果，不用漫长的等待编译了；多端统一，一套实现思路，可以节省测试的成本，不依赖于原生的控件支持，UI风格也能保持统一。

### 二、方案一：以Flutter为主工程，集成Android项目到Flutter中

#### 2.1 实现步骤

* 创建Flutter项目`flutter create flutter_native_app`

  *注意create后面的项目名称不能用大写字母，不然会提示**"..." is not a valid Dart package name.**，比如`flutter create FlutterNativeApp ，这种就是创建不了的*

* 将已有Android项目merge到android目录下

  Application`的`onCreate`中添加初始化

  ```kotlin
  class App:Application() {
      override fun onCreate() {
          super.onCreate()
          FlutterMain.startInitialization(this)
      }
  }
  ```

* 代码参考：http://gitlab.dongyin.net/panmin/flutter_native

### 三、方案二：以Android项目为主工程，集成Flutter到Android项目中

> 参考官方方案：https://flutter.dev/docs/development/add-to-app



### 四、两种方案对比

### 五、闲鱼flutter混合开发方案[flutter-boost](https://github.com/alibaba/flutter_boost)

