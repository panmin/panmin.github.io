## Android原生集成Flutter

### 一、简介

**背景**：本人Android开发，目前公司的项目已经完成组件化，但是组件化带来的一个缺点就是项目编译速度变慢了，开发电脑又不给力，严重影响了开发效率；再者Android和iOS都是用原生开发的，导致同样的业务可能实现的思路不同，也增加了测试的成本；由于机制不同，两端实现的界面风格也不完全统一。

**选择flutter的原因**：编译效率高，有Hot Reload，改为代码就能看到效果，不用漫长的等待编译了；多端统一，一套实现思路，可以节省测试的成本，不依赖于原生的控件支持，UI风格也能保持统一。

### 二、方案一：以Flutter为主工程，集成Android项目到Flutter中

#### 实现步骤

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

> 参考官方方案：https://flutter.dev/docs/development/add-to-app 官方当前版本为**1.12**，本文使用了**1.9**版本

#### 3.1 在Android项目中创建Flutter Module

创建Flutter Module有两种方式：

* android studio中：`File --> New --> New Module --> Flutter Module   `
* 通过命令创建：`flutter create -t module --org com.panmin flutter_module`

#### 3.2 在Android项目根目录的`setting.gradle`添加脚本

```groovy
setBinding(new Binding([gradle: this]))
evaluate(new File(
        settingsDir,
        'flutter_module/.android/include_flutter.groovy'
))
```

#### 3.3 Android项目引用创建的flutter_module

在app的项目`build.gradle`的`dependencies`中同样有两种方式添加引用：

* 直接引用源码

  ```groovy
  implementation project(':flutter')
  ```

* 引用aar包

  1. 先`cd flutter_module`，然后再`flutter pub get`，会生成`.android`和`.ios`项目

  2. 执行`flutter build aar`，会生成release的aar，或者运行`flutter build aar --debug`生成debug的aar

  3. 添加如下maven引用

     ```groovy
     repositories {
         maven { url '../flutter_module/build/host/outputs/repo' }
     }
     dependencies {
         debugImplementation 'com.panmin.flutter_module:flutter_debug:1.0@aar'
         releaseImplementation 'com.panmin.flutter_module:flutter_release:1.0@aar'
     }
     ```

#### 3.4 创建继承`Application`的类App，添加`FlutterMain.startInitialization(this)`

```kotlin
class App:Application() {
    override fun onCreate() {
        FlutterMain.startInitialization(this)
        super.onCreate()
    }
}
```

在`AndroidManifest.xml`中添加`<application android:name=".App"`

#### 3.5 实现跳转`FlutterActivity`

```kotlin
import io.flutter.app.FlutterActivity
......
startActivity(Intent(this, FlutterActivity::class.java))
```

*此处不使用`io.flutter.embedding.android.FlutterActivity` 而使用`io.flutter.app.FlutterActivity`*,**1.9**版本的flutter中的`io.flutter.embedding.android.FlutterActivity`还是处于试验阶段，这个和新版本的**1.12**不太一样，参考：https://github.com/flutter/flutter/wiki/Experimental:-Add-Flutter-Activity

#### 3.6 原生如何跳转到对应的flutter页面

* 继承`FlutterActivity`实现`MainFlutterActivity`

  ```kotlin
  class MainFlutterActivity : FlutterActivity() {
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          GeneratedPluginRegistrant.registerWith(this)
      }
  
      override fun createFlutterView(context: Context?): FlutterView? {
          val name = intent.getStringExtra("NAME") ?: return super.createFlutterView(context)
          val view = FlutterView(this)
          view.layoutParams = ViewGroup.LayoutParams(
              ViewGroup.LayoutParams.MATCH_PARENT,
              ViewGroup.LayoutParams.MATCH_PARENT
          )
          setContentView(view)
          view.setInitialRoute(name)
          return view
      }
  }
  ```

* `AndroidManifest.xml`中声明`MainFlutterActivity`

  ```kotlin
  <activity android:name=".MainFlutterActivity"/>
  ```

* flutter中实现

  ```dart
  class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      return MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: _widgetRouter(ui.window.defaultRouteName),
      );
    }
  
    _widgetRouter(String defaultRouteName) {
      switch (defaultRouteName) {
        case "/":
          return MyHomePage(title: 'Flutter Demo Home Page');
        case "/first":
          return FirstPage();
        case "/second":
          return SecondPage();
      }
    }
  }
  ```

* 原生的调用

  ```kotlin
  btn_first_flutter_page.setOnClickListener {
    val intent = Intent(this, MainFlutterActivity::class.java)
    intent.putExtra("NAME","/first")
    startActivity(intent)
  }
  
  btn_second_flutter_page.setOnClickListener {
    val intent = Intent(this, MainFlutterActivity::class.java)
    intent.putExtra("NAME","/second")
    startActivity(intent)
  }
  ```

  

### 四、两种方案对比

### 五、闲鱼flutter混合开发方案[flutter-boost](https://github.com/alibaba/flutter_boost)

