# pitfalls-android

<img src="logo-pitfalls-android.png" width="" height="200"/>

### 备注：本仓库正在进行格式调整，以便大家有更好的查找预览的格式，请耐心等待。

其他 pitfalls 系列：[https://github.com/42Chapters/pitfalls](https://github.com/42Chapters/pitfalls)

#### 注：新增加 [Exception](https://github.com/42Chapters/pitfalls-android/blob/master/Exception.md) ，用来记录Android 开发中各种异常的背后原因。

***
>**问题格式保持下统一：**    
问题现象：****    
原因分析：****    
解决方法：****   
***

### 71、10月份小知识点记录
1、UrlQuerySanitizer——使用这个工具可以方便对 URL 进行检查。

2、Activity.recreate重新创建Activity。有什么用呢？可以在程序更换主题后，立马刷新当前Activity，而不会有明显的重启Activity的动画。(屏幕会变黑一下)

3、设置TextView单行显示的时候不要用Lines=1,而要用singleLine=”true”,因为魅族部分手机在设置Lines=1的时候，然后TextView的值全为数字的时候， 你就会懵逼了。

4、PageTransformer接口，用来自定义ViewPager页面切换动画，用setPageTransformer(boolean,PageTransformer)方法来进行设置；
android:animateLayoutChanges=”true”，LinearLayout中添加View的动画的办法，支持通过setLayoutTransition()自定义动画。

5、android:descendantFocusability，ListView的item中CheckBox等元素抢焦点导致item点击事件无法响应时，除了给对应的元素设置 focusable,更简单的是在item根布局加上android:descendantFocusability=”blocksDescendants”。
includeFontPadding=”false”，TextView默认上下是有一定的padding的，有时候我们可能不需要上下这部分留白，加上它即可。

6、WeakHashMap，直接使用HashMap有时候会带来内存溢出的风险，使用WaekHashMap实例化Map。当使用者不再有对象引用的时候，WeakHashMap将自动被移除对应Key值的对象。

7、
View.post方便在非UI线程对界面进行修改，与Handler的作用类似。并且由于post的Runnable会保证在该View绘制完成的前提下才调用，所以一般也可以用于获取View的宽高；还有一种是用getViewTreeObserver获取宽高。


### 70、关于修改固件蓝牙名称的问题记录

最近处理蓝牙设备重命名模块时，发现部分 Android 手机出现设备名字修改之后的刷新不及时，这个是由于手机系统本身造成的。建议做法不做处理，遵循系统的刷新逻辑。
由于修改设备重命名时存在多款不同的设备，有些设备不支持重命名，因此建议添加是否支持重命名的判断。

### 69、多线程同时访问集合（ConcurrentModificationException）

**问题现象：** 

多线程同时修改集合时常常容易出现 **ConcurrentModificationException** ，即便是改成用 *Collections.synchronizedCollection()* 方法同步也无效。

**原因分析：**

当集合正在迭代时，如果进行修改就会出现异常，[@问题13]() 已经说过该问题。而 *synchronizedCollection()* 方法虽然对部分操作加上了 *synchronized* 关键字以保证线程安全，但其 *iterator()* 操作不是线程安全的，在迭代时操作依然会出现异常，并且效率也比较低。

**解决方法：**

在 Java 中早已有比较好的替代对象，相比起来有更加细化的锁机制，效率更高，不会出现 *ConcurrentModificationException* 异常。

- **ConcurrentHashMap** 为 Map 的同步，设计与实现非常精巧，很适合[学习](http://www.importnew.com/22007.html)        
- **CopyOnWriteArrayList** 为 List 的同步，采用写入时复制的方式避开并发问题，当修改操作较多时性能上会有比较大的代价   


### 68、Facebook 或 Twitter 自定义登陆按钮样式

**问题现象：** 

使用官方的 SDK 做登陆时，只提供了登陆按钮作为跳转入口，但是样式和设计图有很大出入，直接设置按钮的样式很难达到想要的效果。

**原因分析：** 

官方应该是推荐登陆时使用默认的样式，这样来避免或达到某些目的。虽然尽力想用默认的样式，不过由于与 UI 的其它部分太不搭了，还是决定自定义。

**解决方法：**

先将官方按钮设置为不可见，然后自定义效果样式按钮，并将点击事件实现为触发官方按钮的点击事件，如下：

    <com.twitter.sdk.android.core.identity.TwitterLoginButton
        android:visibility="invisible" />
	
	// 或使用 performClick() 方法
	twitterLoginButton.callOnClick();


### 67、Android 5.0 的通知栏 SmallIcon 的 BUG

**问题现象：**  

	android.app.RemoteServiceException: Bad notification posted from package xxx.xxx.xxx: 
	Couldn't create icon: StatusBarIcon(xxx.xxxx)

**原因分析：** 

算作是 Android5.0 的 Bug，在 android4.4 和 6.0 中都正常。

**解决方法：**

```java
if (Build.VERSION.SDK_INT == Build.VERSION_CODES.LOLLIPOP
	|| Build.VERSION.SDK_INT == Build.VERSION_CODES.LOLLIPOP_MR1){
	iconId = mContext.getApplicationInfo().icon;
}
NotificationCompat.Builder.setSmallIcon(iconId)
```


### 66、ScrollView与RecyclerView嵌套后的冲突问题

**问题现象：** 
我们以前在使用GridView,ListView与ScrollView嵌套时一定有遇到显示不全或者滑动冲突等问题，
在我们的RecyclerView使用中仍然难逃此劫，甚至有的时候会有卡顿的问题。下面我们介绍一下如何解决这个问题

**原因分析：**   

同理与ListView与GridView相同

**解决方法：**

#### 1、卡顿，滑动不流畅问题。

>首先解决卡顿的问题使用**mRecyclerView.setNestedScrollingEnabled(false);**                          
>这个目前是最优解


#### 2、显示不全（6.0以上容易出现此问题）
> 方法一：在Recycler的父布局中添加RelativeLayout隔离，不用像一些博客说的那样进行高度计算和         OnMeasured()重写。（未解决尝试方式二）  
         
> 方法二：将你的ScrollView替换成android.support.v4.widget.NestedScrollView，此方法对此问题有优化（未解决尝试方式三）  
         
> 方法三：如上还未解决或者只显示一行，把design库和V7库升级到23.2以上，然后加上如下代码
>  mLinearLayoutManager.setSmoothScrollbarEnabled(true);  
   mLinearLayoutManager.setAutoMeasureEnabled(true);  
   mRecuclerView.setLayoutManager(mLinearLayoutManager);  
   mRecuclerView.setHasFixedSize(true);  
   **mRecuclerView.setNestedScrollingEnabled(false);**  

> 如果还未解决，或者只显示一行，你可以核对一下你的适配器子布局中高度是否使用了
android:layout_height="match_parent"

### 65、Android Studio 3.0 编译项目无法找到 Gradle

问题现象：  
build.gradle 文件：

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0-alpha3'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
allprojects {
    repositories {
        jcenter()
        maven { url 'https://jitpack.io' }
    }
}
task clean(type: Delete) {
    delete rootProject.buildDir
}
```

Android Studio 3.0 Canary 3 编译项目提醒：

```
Error:Could not find com.android.tools.build:gradle:3.0.0-alpha2.
Searched in the following locations:
    file:/Applications/Android Studio 3.0 Preview.app/Contents/gradle/m2repository/com/android/tools/build/gradle/3.0.0-alpha2/gradle-3.0.0-alpha2.pom
    file:/Applications/Android Studio 3.0 Preview.app/Contents/gradle/m2repository/com/android/tools/build/gradle/3.0.0-alpha2/gradle-3.0.0-alpha2.jar
    https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0-alpha2/gradle-3.0.0-alpha2.pom
    https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0-alpha2/gradle-3.0.0-alpha2.jar
Required by:
    project :
```

原因分析：找不到，可能是网络或者服务器问题，最终定位到是 Google 更新了针对 Android Gradle 编译的仓库地址有更新。

解决方法：我们需要在 build.gradle 文件中追加：

```
repositories {
   maven {
       url "https://maven.google.com"
   }
}
```

官方文档说明：  
[https://android-developers.googleblog.com/2017/05/android-studio-3-0-canary1.html](https://android-developers.googleblog.com/2017/05/android-studio-3-0-canary1.html)


### 64、编译时出现jar包内包含相同的文件

问题现象：
我在项目中添加了一些jar的引用，但在编译的时候发现存在相同的文件，导致编译失败。    
原因分析：   

> Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
> com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/***.properties 

解决方法：
如果依赖的jar使用多次引用版本不同的jar包，那么最好的办法是选择留下最合适的版本jar包。小概率是因为使用不是相同的jar包时出现了文件冲突问题，这时候在build.gradle中添加packagingOptions就能解决。

	packagingOptions { 
	     exclude 'META-INF/***.properties      }

### 63、关于so库使用小总结
   
问题现象：
应用中新添加了so库，在32位处理器上没有问题，跑64处理器的时候出现了UnsatisfiedLinkError的问题，查看了异常是lib64的so库没有找到，之后添加了ndk的配置后，成功在64处理器上跑通，但发现library里的so库受到影响，加载失败。    
原因分析：
> java.lang.UnsatisfiedLinkError:dalvik.system.PathClassLoader
> java.lang.UnsatisfiedLinkError:No implementation found for void io.vov.... MedioPlayer.native_init()

第一个异常里面有lib64 so 没有发现，直接找64和32位的问题，第二个
问题指向的是一个native方法init失败，打包apk发现只有armeabi中的so库，而library中为了优化性能和文件大小，只在armeabi留下一个库，其他都放在了其他文件夹中。	
解决方法：
处理64位兼容上，使用了兼容armeabi的方法，在build.gradle中添加了

	   ndk {
	    abiFilters "armeabi"
	    } 

由于library中armeabi只有一个so库，所以加载失败了。原因是指定要ndk需要兼容的架构(这样其他依赖包里mips,x86,armeabi,arm-v8之类的so会被过滤掉)，所以在上面添加了

		ndk {
		abiFilters "armeabi", "x86"，"armeabi-v7a"
		}

这样就能兼容我使用的库中的所有so，但还是担心后面有加载库失败的问题，查看了网上abi的资料，没问题。
在项目只包含了 armeabi，那么在所有Android设备都可以运行； 如果项目只包含了 armeabi-v7a，除armeabi架构的设备外都可以运行； 如果项目只包含了 x86，那么armeabi架构和armeabi-v7a的Android设备是无法运行的； 如果同时包含了 armeabi， armeabi-v7a和x86，所有设备都可以运行，程序在运行的时候去加载不同平台对应的so，这是较为完美的一种解决方案，同时也会导致包变大。


### 62、Google Play Store 过滤问题总结

说明：当用户在 Google Play 上搜索或浏览应用以下载时，会根据哪些应用与其设备兼容来过滤搜索结果。例如，如果应用需要摄像头，Google Play 不会在没有摄像头的设备上显示该应用。这种过滤帮助开发者管理其应用的分发，并且有助于确保为用户提供最佳的体验

过滤规则 ： [https://developer.android.com/google/play/filters.html?hl=zh-cn](https://developer.android.com/google/play/filters.html?hl=zh-cn)
>注：需要硬件支持的权限也会默认进行设备兼容过滤；


如何去掉指定功能设备兼容过滤 ：

Nexus 7 没有电话功能 ，然而在应用的 Manifest 文件里声明了电话相关的权限，希望不支持电话功能的设备也能搜索该 APP
，就在 Manifest 文件添加如下代码 ：

	<uses-feature android:name="android.hardware.telephony" android:required="false"/>

>注：通过显式声明某项功能并加入 android:required="false" 属性，可以在 Google Play 上有效停用所有针对指定功能的过滤。

更多功能声明 ： [https://developer.android.com/guide/topics/manifest/uses-feature-element.html?hl=zh-cn#permissions-features](https://developer.android.com/guide/topics/manifest/uses-feature-element.html?hl=zh-cn#permissions-features)

### 61、媒按键监听，Android5.0+ 不同的监听方式

**使用场景描述：**
应用中需要播放音乐的时候，通常有个令人捉急的问题就是媒体焦点；假如同时用 QQ音乐 与自己的应用同时播放音乐的时候，媒体焦点到底花落谁家，谁能够响应这次媒体按键；这就要看谁最后申请了这个焦点

以往的方式都是通过广播的形式，来看看 Android 5.0+ 的注册方式

		 int result = audioManager
                .requestAudioFocus(onAudioFocusChangeListener,
                        AudioManager.STREAM_MUSIC, AudioManager.AUDIOFOCUS_GAIN);

        if (AudioManager.AUDIOFOCUS_REQUEST_GRANTED == result) {

            if (android.os.Build.VERSION.SDK_INT >= 21) {
                //注册媒体按键 Android 5.0+
                 session = new MediaSession(mContext, "tag");
				session.setCallback(new MediaSession.Callback() {
					//这里重写 播放/暂停，上下一曲的回调 ，按键都走这里的回调			
				}

            } else {
                //通常的注册媒体按键 ，广播的方式
                audioManager.registerMediaButtonEventReceiver(mComponentName);
            }

        }
>Android 5.0+ 如果用以往广播的方式注册，焦点抢不过来；

详细代码请参考：[Android 注册媒体按键监听](http://www.jianshu.com/p/b52754c50f89)

### 60、Android 6.0+设备无法获取`WRITE_EXTERNAL_STORAGE`权限

**描述：** Android6.0+以后，系统对权限管理模块进行的升级，并且对权限进行的分类，其中`WRITE_EXTERNAL_STORAGE`为危险权限。

**问题：** Android6.0+的手机上使用录音功能后播放无声音，此时用户点击了允许录音的权限，但是仍然无法录音。

**解决：** 通过现象排查，由于出现此问题的手机Android系统均为6.0+，而6.0+的手机在权限这块改定较大，通过调试发现此问题的原因是无法获取WRITE_EXTERNAL_STORAGE权限，导致录音文件未被写入SD卡，以至于播放录音无声音；**将文件存储路径改为/data/data/包名/cache/;** 解决此问题，因为此路径为内置路径，在6.0系统（ API > 23 ）时，不需要申请权限就可以向这个目录写入文件。

### 59、解决传说中的 Android 65k 问题

在 Android 开发中，有一个之前很少听说，最近偶尔江湖传闻听到过的问题，就是 65k 问题。什么是65k问题呢？其实很简单，就是 Android 有个限制，你的每个 App 中函数最多只能有 65536 个。

这个限制其实是这样的，因为在编译成 Dalvik 字节码，也就是把你的 Class 们生成打包到一个 classes.dex 中去的时候呢，编译器会给你的 App 中所有的函数方法指定一个 ID， 然后每一个 classes.dex 中 ID 的范围是 [0, 0xffff] 。 所以，你懂的，就有了那么一个 65k 的问题。

**编译报错现象**

	Error:Execution failed for task ':app:transformClassesWithDexForDebug'.
	> com.android.build.api.transform.TransformException:
	> com.android.ide.common.process.ProcessException: org.gradle.process.internal.ExecException: Process 'command 'C:\Program Files\Java\jdk1.8.0_51\bin\java.exe'' finished with non-zero exit value 2

**解决方法（针对Android Studio）：** 在build.gradle中对应的地方添加
multiDexEnabled true 这句代码就行了，如下
	
	android {
		
    	defaultConfig {
        	multiDexEnabled true
		}

		......

	}

### 58、 android 安装包过大
关于发现 android 安装包过大、想优化安装包大小时，应该先确认是哪些文件使安装包过大，可以先把安装包解压，然后查看各个目录的大小，再在目录底下确认是否引用了比较大的文件(如字体、图片等)。如某些图片过大，可以使用工具进行压缩下(如 pngyu )。如果是 **res** 文件夹太大，可以减少不必要的图片资源，国际化相关的图片不需要在每个文件夹都放入，只需要使用没有文字的图片，文字可以通过代码添加。而屏幕适配相关的图片能用 **.9** 图片去实现就用 **.9** 图片去实现。 **res** 文件夹太大也可能是存在太多无用的资源，可以使用 **Android lint** 工具去检查，然后将无用资源文件统统删除。字体文件也可能是导致安装包过大的原因，可以用工具将需要使用的字体保留(如英文、数字等)，其他的去除形成新的字体文件。如果使用字体的地方比较少也可使用图片的形式去代替字体文件。项目中没用到的架包也需及时删除。关于一些比较大，但又必须的文件可以先不将其放入工程内，后面采用网络的方式加载。如果发现不了原因可以比较项目不同的版本，来确定是那一次版本的更新使安装包过大，再通过更新的文件来检查原因。


### 57、BLE中心设备的 **onCharacteristicChanged()** 方法没有回调

**描述：** 当设备为 **Indication** 模式时，设备的值有变化时会主动返回给App，App在 **onCharacteristicChanged()** 方法中能收到返回的值。

**问题：** 在App中通过如下代码注册监听，注册成功后就能接收到设备主动反馈的值了。然而以下代码执行后依旧收不到反馈。   

	bluetoothGatt.setCharacteristicNotification(characteristic, true)

**解决：** 当上面的方法执行返回true后，还要执行如下的代码才能注册成功。（[完整代码](http://blog.csdn.net/u010134293/article/details/53422349)）

	for(BluetoothGattDescriptor dp: characteristic.getDescriptors()){
		if (dp != null) {
			if ((characteristic.getProperties() & BluetoothGattCharacteristic.PROPERTY_NOTIFY) != 0) {
				dp.setValue(BluetoothGattDescriptor.ENABLE_NOTIFICATION_VALUE);
			} else if ((characteristic.getProperties() & BluetoothGattCharacteristic.PROPERTY_INDICATE) != 0) {
				dp.setValue(BluetoothGattDescriptor.ENABLE_INDICATION_VALUE);
			}
			gatt.writeDescriptor(dp);
		}
	}



### 56、Android 6.0 动态请求权限

**描述：** Android 应用在访问额外的资源或信息时，需要请求相应权限。根据权限的敏感性，系统可能会自动授予权限，或者由用户对请求进行许可。Android6.0及以上应用除了在清单文件中声明权限，敏感权限还需要在用户使用时动态授予。官方定义了[普通和危险权限](https://developer.android.com/guide/topics/security/permissions.html#normal-dangerous)，经测试发现**部分手机厂商的敏感权限会有所差异** 。   

**问题：** 应用中用到 **READ\_PHONE\_STATE** 权限来获取设备ID，在华为、小米的6.0系统的手机上运行都可以正常获取，然而在Nexus5X上获取失败而导致应用闪退。

**原因：** 华为、小米等系统会默认允许该权限，而官方定义该权限为危险权限默认不被允许。   

**解决：**   
>1，将 **targetSdkVersion** 版本号调整为 **23** 以下，这样应用不需要在运行时请求权限，系统会默认允许清单文件中所有权限。   
>
>2，在应用中向用户动态请求权限，请求方式可参考 **[52、Android6.0扫描不到蓝牙设备的处理办法](#SCAN_BLUETOTH_PERMISSION)**。更好的做法是使用Github上的第三方权限请求库。


### 55、事件分发处理总结
在我们Android程序中，除单一控件（继承ViewGroup）的，例如TextView,Button等是无法拦截事件外，其它View均可对事件进行分发，拦截以及向上传递。其中事件分发的事件dispatchTouchEvent。当我们重写此方法时，可以接收父类传递下来的事件，传递true，消费此事件，否则会继续向下传递。

在传递过程中我们也可以通过子类对它进行拦截，方法时重写onInterceptTouchEvent方法，返回true拦截，false不拦截。

如果整个触摸事件过程中，所有事件均未对它拦截，那么他的事件最终也会通过onTouchEvent从最下层的控件逐步向上传递，最终返回到Activity或Fragment的onTouchEvent中。

在整个事件分发中，我们也可以通过getParent或getChildren拿到父类或子类事件进行处理。

常见事件分发冲突案例：ViewPager嵌套Fragment过程中，在其中一个Fragment中有轮播图时，会照成事件冲突,或滑动偏移等情况。

解决方案：自定义Viewpager,重写onTouchEvent，当用户执行down与move事件时，事件分发给轮播图的控件处理，当执行到up与cancle事件时，在将事件重新传给viewPager处理。不过通过测试Android Studio中最近的v4包中好像已经修复了此问题。

### 54、生命周期引起的监听问题
部分开发者在注册监听时，经常会在注册监听后在Activity或Fragment销毁时未移除此监听。

如果是单一的监听，可能仅仅只是造成内存泄漏的情况，至少用户感知不到，但是如果我们在一个父类中写一个监听，同时有2个子类注册了此监听，
并在父类中有弹出Dialog或者刷新UI的操作时，当其中一个子类被finish掉后，其实例被回收，但其监听仍然会存在。这会引起当我们在另外一个未被finish的实例中去做跟监听相关的操作会，由于之前的实例被finish,监听又存在，回走多次回调，并且很有可能会导致空指针等一系列导致程序崩溃的异常。如果子类的监听无穷多，甚至会导致OOM。

建议，在实例销毁时一定要及时的移除没必要的监听，竟可以节约内存，也可以避免在后期添加需求时代码的耦合性。




### 53、Android细节点总结

1.timePicker点击确定后需要clearFoucs才能获取手动输入的时间。

2.Handler在子线程使用Looper.prepare,或者new的时候给构造函数传入MainLooper来确保在主线程run。

3.ExpandableListView的子列表不能点击（禁用）要把Adapter的isChildSelectable方法返回true。

4.注意按钮的感应范围不小于9mm否则不易点击；输入框注意光标的位置更易用互输入。

5.服务器和客户端尽量统一唯一标识（有可能是ID），否则多少会有歧义和问题。

6.activity的finish方法中使用了synchronized (this)，所以activity的方法尽量不要使用 synchronized来修饰，或者有 synchronized (this)修饰的方法块，因为这些方法或者方法块一旦存在耗时操作，会导致finish方法无法执行，从而造成ANR。

7.PopupWindow中的EditText点击和长按的时候是没有复制，黏贴，全选这些选项弹出来的，这是android的一个系统bug，可以使用Dialog替代PopupWindow来达到同样的效果。

8.如果TextView的text含有特殊字符，使得text不靠TextView的左边显示，可以通过强制设置gravity为left来解决。

9.
Android中animation自从开始起作用后，就缓存到了某个地方，只管不停的绘制，哪怕自己都不存在了，都还在那绘制，clearAnimation的作用就是通知一下他，你都没了，别再画了（比如一个button，startAnimation后没有clearAnimation，你点击的话很难相应，那就是因为这个button一直在不停的绘制，你点击的时候一直获取不到焦点）。

10.
Android中animation对于目标view的位置实际上是没有改变的，当android:fillAfter="true"时，动画结束后view停在动画最后一祯的位置。

###  [52、Android6.0扫描不到蓝牙设备的处理办法](#SCAN_BLUETOTH_PERMISSION)

描述：在Android6.0手机上扫描不到蓝牙设备（如Nexus6），并会抛出一个异常：

    java.lang.SecurityException: Need ACCESS_COARSE_LOCATION or ACCESS_FINE_LOCATION permission to get scan results

解决办法：   
1，在清单文件加入权限：

	<uses-permissionandroid:name="android.permission.ACCESS_FINE_LOCATION"/> 
	<uses-permissionandroid:name="android.permission.ACCESS_COARSE_LOCATION"/>

2，在Activity中调用  requestPermissions() 方法来请求权限，系统会弹出需要请求权限的对话框   
3，重写Activity的onRequestPermissionsResult()方法，接收权限是否请求的请求状态    
示例代码如下：

	private static final int PERMISSION_REQUEST_COARSE_LOCATION = 1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ......
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            // Android M Permission check
            if (this.checkSelfPermission(Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
                requestPermissions(new String[]{Manifest.permission.ACCESS_COARSE_LOCATION}, PERMISSION_REQUEST_COARSE_LOCATION);
            }
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {
        switch (requestCode) {
            case PERMISSION_REQUEST_COARSE_LOCATION:
                if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    // TODO request success
                }
            break;
        }
    }


###  51、Android输入法弹出，设置控件在软键盘之上显示

描述：当输入法弹出时，布局会被压缩，某些控件被遮挡住，但是需求可能并不想让该控件遮挡住。如何让某些控件也浮在软件盘上，比如“登录”按钮。   
解决：给最外层的布局设置一个OnGlobalLayoutListener的监听事件，当布局发生改变时改变控件位置的方式来实现，代码如下：

		/**
		* @param root 最外层布局，需要调整的布局
		* @param scrollToView  被键盘遮挡的scrollToView，滚动root,使scrollToView在root可视区域的底部
		*/
		private void controlKeyboardLayout(final View root, final View scrollToView) {
			// 注册一个回调函数，当在一个视图树中全局布局发生改变或者视图树中的某个视图的可视状态发生改变时调用这个回调函数。
			root.getViewTreeObserver().addOnGlobalLayoutListener(
		        new ViewTreeObserver.OnGlobalLayoutListener() {
		            @Override
		            public void onGlobalLayout() {
		                Rect rect = new Rect();
		                // 获取root在窗体的可视区域
		                root.getWindowVisibleDisplayFrame(rect);
		                // 当前视图最外层的高度减去现在所看到的视图的最底部的y坐标
		                int rootInvisibleHeight = root.getRootView().getHeight() - rect.bottom;
		                // 若rootInvisibleHeight高度大于100，则说明当前视图上移了，说明软键盘弹出了
		                if (rootInvisibleHeight > 100) {
		                    //软键盘弹出来的时候
		                    int[] location = new int[2];
		                    // 获取scrollToView在窗体的坐标
		                    scrollToView.getLocationInWindow(location);
		                    // 计算root滚动高度，使scrollToView在可见区域的底部
		                    int srollHeight = (location[1] + scrollToView.getHeight() + PixelUtil.dp2px(20, getContext())) - rect.bottom;
		                    if (srollHeight != 0){
		                        root.scrollTo(0, srollHeight);
		                    }
		                } else {
		                    // 软键盘没有弹出来的时候
		                    root.scrollTo(0, 0);
		                }
		            }
		        });
		}



###  50、使用fragmentManager.add()方法打开新页面时，界面不更新保持在原界面上

描述：调用add()方法切换新页面后，界面并没有替换，依旧停留在原来的页面上，而调用replace()方法时可以正常替换     
原因：替换是在LinearLayout布局中，新添加的布局显示在屏幕以外所以感觉是没有开启新的页面。可以改成使用FrameLayout。   

区别: add 是把一个fragment添加到一个容器 container 里。replace 是先remove掉相同id的所有fragment，然后在add当前的这个fragment。在使用add()的情况下，这个container其实有多层，多层肯定要比一层的来得浪费，所以还是推荐使用replace()。   


###  49、沉浸式状态栏对SlidingMenu无效的问题
针对Andorid4.4以及5.0以后，更多的APP更亲耐于沉浸式状态栏，无论从视觉和体验上都以成主流，但这种沉浸式的状态栏并未天衣无缝，由于Android 中SlidingMenu有继承与ViewGroup,ViewPage等，由于源码冲突，使用普通的方法无论是设置主题，还是通过代码设置setTranslucentStatus实测均对SlidingMenu无效。通过对SlidingMenu源码简单分析，换一种概念的来实现这种沉浸式的效果。
简单来说分为4步来设置SlidingMenu无效的问题：

1、取消状态栏修改颜色

	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            activity.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);//去掉信息栏
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN);//显示状态栏
			setTranslucentStatus(activity, true);
		}
		

2、在需要沉浸的UI父布局中添加如下属性,注意是必须添加

	android:fitsSystemWindows="true"  
	
此时状态栏如果为白色，则前面2步OK，否则rockback;

3、将整体布局往上移一个状态栏的高度，将布局顶上去

	@TargetApi(19)
    	public static void setTranslucentStatus(Activity activity,boolean on) {
        	Window win = activity.getWindow();
        	WindowManager.LayoutParams winParams = win.getAttributes();
        	final int bits = WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS;
        	if (on) {
            		winParams.flags |= bits;
        	} else {
            		winParams.flags &= ~bits;
        	}
        	win.setAttributes(winParams);
    	}
    	
 4、通过子布局上方显示与隐藏来控制上边距
 
  //状态栏显示隐藏设置
    public static void setStatusBarViewVisibility(View view) {
        if (view == null) {
            return;
        }
        //注释掉状态栏view
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
			view.setVisibility(View.VISIBLE);
		} else{
			view.setVisibility(View.GONE);
		}
    }
    
以上方法在项目中亲测，有效。


###  48、ListView中数据与界面显示不同步导致的BUG
异常日志如下：   
	java.lang.IllegalStateException: The content of the adapter has changed but ListView did not receive a notification. Make sure the content of your adapter is not modified from a background thread, but only from the UI thread. Make sure your adapter calls notifyDataSetChanged() when its content changes.     
我代码中产生的原因是由于ListView数据设置的是ListA的引用，而ListA会经常变动，但界面没有跟随刷新导致。給几个避免该问题的解决办法：

- 确保Adapter的数据更新后一定要调用notifyDataSetChanged()方法通知ListView   
- 数据更新和notifyDataSetChanged()放在UI线程内，且必须同步顺序执行，不可异步   
- 我是通过将ListView的数据与源数据隔离，如通过“new ArrayList<>(ListA)”的方式与源数据分离

###  47、自定义View中绘制文字居中的问题

问题说明：在自定义View中，获取文字的高宽，并不能完全的居中，关键代码如下：

    mFontPaint.getTextBounds(text, 0, text.length(), fontRect);  
    // 设置文字左下角的起始点坐标，绘制文字  
    canvas.drawText(text, centerX-fontRect.width()/2, centerY+fontRect.height()/2, mFontPaint);  

上面方式不能居中的原因主要在于文字并不是在它的方格正中间绘制的，而是以Baseline作为参考线绘制的。在计算文字的高度时，应该是文字的顶部减去底部的值，通过**Paint.getFontMetrics()**获取一个FontMetrics对象，再用它的bottom-top。设置文字居中可以用**Paint.setTextAlign(Paint.Align.CENTER)**方法。示例代码如下：

	mPaint.setTextAlign(Paint.Align.CENTER);  
    Paint.FontMetrics fontMetrics= mPaint.getFontMetrics();  
    float top = fontMetrics.top;  
    float bottom = fontMetrics.bottom;
	canvas.drawText(text,centerX,centerY+(bottom-top)/2,mPaint); 
详情参考：[自定义View中文本居中显示](http://blog.csdn.net/u010134293/article/details/52335882)


###  46、生命周期引起的问题：

谨慎使用Android的透明主题，透明主题会导致很多问题，比如：如果新的Activity采用了透明主题，那么当前Activity的onStop方法不会被调用；在设置为透明主题的Activity界面按Home键时，可能会导致刷屏不干净的问题；进入主题为透明主题的界面会有明显的延时感；

当前Activity的onPause方法执行结束后才会执行下一个Activity的onCreate方法，所以在onPause方法中不适合做耗时较长的工作，这会影响到页面之间的跳转效率；

### [45.Android Jackson、Gson、FastJson解析框架总结](http://blog.csdn.net/zhanggang740/article/details/52278373)
1、比较来说, Gson 比 fastjson 考虑更全面, 对用 URL , UUID, BIT_SET, CALENDAR 等等,都有特定的输出规则.
 
2、小数量的调用 Gson 比 fastjson 快一点. (几十毫秒,可以毫不在意.猜测是因为 javassist 生成新的 Wrapper 类导致,因为还要编译的.)
 
3、大数量的调用 fastjson 比 Gson 快. (千万级别的.还不太确定为什么会变快, 猜测是 gson 的反射调用,毕竟比不上 fastjson Wrapper 类的真实调用.)

4、代码可阅读性: fastjson 比 Gson 好很多很多.
 
5、fastjson 在要序列化对象的类型的判断上,使用的是 if else 。
 
6、Gson 使用的是遍历 TypeAdapterFactory集合,在每个 TypeAdapterFactory 里面做判断.而且使用了 N 多的匿名内部类, 想要一眼看出有哪些 TypeAdapterFactory 的实现都很困难.
 
7、如果普通日常使用,推荐使用 fastjson,简单易懂,并且是国内程序员开发,有问题可以较容易的获得支持.
 
8、Gson 有对各种类型的属性支持, 如果有特殊类型json化需求或复杂结构时可以选择 gson ,并自定义扩充.
 
9、如果你不需要对JSON文档进行按需解析、且性能要求较高的话，可以尝试使用Jackson.

### 44.ListView , GridView OnItemClickListener事件无响应
分析:  
在Android软件设计与实现中我们通常都会使用到ListView这个控件,系统有一些预置的Adapter可以使用,例如SimpleAdapter和ArrayAdapter,但是总是会有一些情况我们需要通过自定义ListView来实现一些效果,那么在这个时候,我们通常会碰到自定义ListView无法选中整个ListViewItem的情况,也就是无法响应ListView的onItemClickListener中的onItemClick()方法

解决方法:  
可以通过对Item Layout的根控件设置其android:descendantFocusability=”blocksDescendants” , 这样Item Layout就屏蔽了所有子控件获取Focus的权限

注意：这个属性不能设置给ListView，设置了也不起作用 ; 

### 43.[三星手机从相册选择图片会旋转问题完美解决](http://blog.csdn.net/lyhhj/article/details/48995065)
问题说明: 项目中有上传图片的功能，那么涉及到拍照，从相册中选择图片，别的手机都ok没有问题，唯独三星的手机拍照之后，你会很清楚的看到会把照片旋转一下，然后你根据路径找到的图片就是已经被旋转的了;
只要在从相册中选择到照片之后获取旋转角度,然后旋转图片就完美解决了.	
代码如下 ：	
先获取到照片的旋转角度 ;

	/**
	*读取照片exif信息中的旋转角度 
     	* @param path 照片路径 
     	* @return角度
     	*/
    public static int readPictureDegree(String path) {  
        int degree  = 0;  
        try {  
            ExifInterface exifInterface = new ExifInterface(path);  
            int orientation = exifInterface.getAttributeInt(ExifInterface.TAG_ORIENTATION, ExifInterface.ORIENTATION_NORMAL);  
            switch (orientation) {  
                case ExifInterface.ORIENTATION_ROTATE_90:  
                    degree = 90;  
                    break;  
                case ExifInterface.ORIENTATION_ROTATE_180:  
                    degree = 180;  
                    break;  
                case ExifInterface.ORIENTATION_ROTATE_270:  
                    degree = 270;  
                    break;  
            }  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
        return degree;  
    }  
    
然后我们只需要根据旋转角度将图片旋转过来就OK了
	
	/**
	 * 旋转图片
	 * @img 需要旋转的 图片
	 * @rotate 旋转的角度
	 * */
	public static Bitmap rotateBitmap(Bitmap img, int rotate) {
		Matrix matrix = new Matrix();
		matrix.postRotate(rotate);
		int width = img.getWidth();
		int height = img.getHeight();
		img = Bitmap.createBitmap(img, 0, 0, width, height, matrix, true);
		return img;
	}


### 42.Android七月份细节点分享
1.与Activity通讯使用Handler更方便；如果你的框架回调链变长，考虑监听者模式简化回调。

2.有序队列操作add、delete操作时，注意保持排序，否则你会比较难堪哦。

3.数据库删除数据时，要注意级联操作避免出现永远删除不了的垃圾数据。

4.关于形参实参：调用函数时参数为基本类型传的是值，即传值；参数为对象传递的是引用，即传址。

5.listview在数据未满一屏时，setSelection函数不起作用；ListView批量操作时各子项和视图正确对应，可见即所选。

6.setSelection不起作用尝试smoothScroollToPosition. ListView的LastVisiblePosition（最后一个可见项）会随着getView方法执行位置不同变动而变动。

7.控制Activity的代码量，保持主要逻辑清晰，其它类保持遵守SRP（单一职责），ISP（接口隔离）原则。

8.arraylist执行remove时注意移除int和Integer的区别。

9.Log请打上Tag，调试打印一定要做标记，能定位打印位置，否则尴尬的是：不知道是哪里在打印。

10.代码块/常量/资源可以集中公用的一定公用，即使公用逻辑稍微复杂一点也会值得，修改起来很轻松，修改一种，到处有效。

### 41.部分华为6.0系统手机闹钟锁屏以及延时的问题总结：

通过调试和现象确认，在华为Mate8，P9上确实会概率性出现一键桌面锁屏后闹钟不响（需唤醒屏幕），以及闹钟延时的问题。
针对这一问题，我们通过Log初步排查，首先排除是固件问题，通过观察测试demo的情况，排除了APP逻辑问题。
其次，通过debug、log日志排查，排除了APP端问题。

通过以上排除法，以及在小米、魅族、Nexus上运行的现象，最终确定此问题为华为Android 6.0定制系统所造成的问题，设置
闹钟后，华为Android 6.0定制系统的安全软件以及手机为了保护电量与安全等，会选择性暂停一些服务，其中包括我们的闹钟服务。
	
### 40.Butterknife:8.2.1后运行导致的空指针问题
Butterknife:8.2.1相对于原来的Butterknife:7.1.0来说，使用起来更方便，效率上也有一定的提升，但是在使用的过程中要注意：
Butterknife:8.2.1之后引用了“android-apt”这个插件，我们在使用的过程中也必须引用，否则在调用其注解下的View时会报空指针异常。
解决办法：

	1、第一步
	buildscript {
  		repositories {
    	mavenCentral()
   	}
  	dependencies {
    	classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  		}
	}
	2、第二步
	apply plugin: 'android-apt'

	android {
 	 ...
	}

	dependencies {
  		compile 'com.jakewharton:butterknife:8.2.1'
  		apt 'com.jakewharton:butterknife-compiler:8.2.1'
	}

提示：如果不引用android-apt也能使用，但是会有异常。

### 39.APP启动闪黑屏的解决办法    
在启动APP时，由于Activity需要跑完onCreate和onResume才会显示界面，就会导致闪黑/白屏。即便是把初始化的工作尽量减少，但由于解析界面还是需要一定时间，黑屏也还是会存在。可以通过下面两种方式尽量减少黑屏的出现：

	// 1,设置背景图Theme
	<style name="Theme.AppStartLoad" parent="android:Theme">  
	    <item name="android:windowBackground">@drawable/bg</item>  
	    <item name="android:windowNoTitle">true</item>  
	</style>
	// 2,设置透明Theme
	<style name="Theme.AppStartLoadTranslucent" parent="android:Theme">  
	    <item name="android:windowIsTranslucent">true</item> 
	    <item name="android:windowNoTitle">true</item>  
	</style>
第一种方式的启动快，界面先显示背景图，然后再刷新其他界面控件，给人刷新不同步感觉。    
第二种方式给人程序启动慢感觉，界面一次性刷出来，刷新同步。


### 38.WebView关闭后，音乐不停的解法方法 
在WebView关闭后，发现音乐还在后台播放，调用“webView.onPause()”也并没有什么用。     
有效的解决方法，第一种是可以加载一个空白页：

	webView.loadUrl("about:blank");
但是下次打开时WebView默认加载的是一个空白页，效果不是很理想，那就调用重新加载页面吧：

	webView.reload();

第二种解决方法(在webView关闭之前销毁) ：

	webView.onDestory();
	

### 37.Zxing很难识别扫描到的二维码的问题
在GitHub上下载了一个二维码扫描的Demo，但用来识别自己屏幕上的二维码时发现怎么也识别不出来，但是用其它的二维码扫描工具很快就识别出来了。      
最后发现是设置了一个比较低分辨率的图片去解析导致的，将分辨率调高后问题就解决了。


### 36.关于使用AlarmManager设置闹钟延时的问题。
**问题**：    
部分手机设置闹钟时，存在延时的情况。    
**问题原因：**    
自从Android API 19(也就是Android4.4系统)开始，触发设置的系统闹钟时间是不准确的:可以保证的是闹钟不会提前触发，但有可能会延迟一些时间。Android 操作系统之所以使用这种策略，是因为要批量去唤醒系统闹钟，最大限度地减少唤醒设备的次数，以更有效的节省电量。    

也就是说目前Android 系统本身基于一些考虑，对系统闹钟的设置做了一些优化调整。不过Android 4.4系统之前的手机应该不存在此类问题。    

**解决方案**：    
代码如下：    
    
    if (apiLevel >= 19) {    
        alarmManager.setExact(type, triggerAtMillis, pendingIntent);
    } else {
	    alarmManager.set(type, triggerAtMillis, pendingIntent);
    }  
		    
**参考官方文档**        
[https://developer.android.com/reference/android/app/AlarmManager.html](https://developer.android.com/reference/android/app/AlarmManager.html)
    

### 35.Android 6月份细节点分享
1.全部Activity可继承BaseActivity，便于统一风格与处理公共事件，构建对话框统一构建器的简历，万一需要整体变动，一处修改到处有效。

2.数据库表字段常量和SQL逻辑分离，更清晰，建议使用Lite系列框架LiteOrm库，超级清晰且重心可以放在业务上，不用关心数据库细节。

3.全局变量放全局类中，私有模块放在自己的常量清晰且集中。

4.不要相信庞大的管理类的东西会带来什么好处，可能是一场灾难，而要时刻注意单一职责原则，一个类专心做好一件事情更为清晰。

5.如果数据没有加载的必要，数据请务必延迟初始化，谨记为用户节省内存，总不会有坏处。

6.异常抛出，在合适的位置处理或者集中处理，不要搞得到处都是catch，混乱且性能低，尽量不要在循环中捕获异常，以提升性能。

7.地址引用链长时（三个以上指向）小心内存泄漏，和警惕堆栈地址指向，典型的易发事件是：数据更新了，ListView视图却没有刷新，这时Adapter很可能指向的并不是你更新的数据容器地址。

8.信息同步：不管是数据库还是网络操作，新插入的数据注意返回ID（如果没有赋予唯一ID），否则相当于没有同步。

9.多线程操作数据库时，db关闭了会报错，也很有可能出现互锁的问题，推荐使用事务。

10.做之前先考虑那些可以公用，资源，layout，类，做一个结构、架构分析以加快开发，提升代码的可复用度。


***

### 34,LinearGradient水平或垂直渲染

在所有的Demo中看到的LinearGradient 渲染的效果都是从左上渲染到右下，那么要怎样调整渲染角度呢。构造如下： 
 
	public LinearGradient (float x0, float y0, float x1, float y1, int[] colors, float[] positions, Shader.TileMode tile);   

- 水平渲染：x0和x1的值相同
- 垂直渲染：y0和y1的值相同
- 其它角度根据调节x、y的值来转变

### 33,Matrix.setRotate(float degrees, float px, float py)注意
	
- degrees 旋转的角度
- px    旋转的X轴坐标，**X为Bitmap的相对坐标**
- py    旋转的Y轴坐标，**Y为Bitmap的相对坐标**

例如要以图片的左下角为重心旋转90度，代码为：matrix.setRotate(90, 0, bitamp.getHeight())  

### 32,java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState异常
	

异常以及其解决方法如下：  
1，getSupportFragmentManager().beginTransaction().commit()方法在Activity的onSaveInstanceState()之后调用    
    解决方法：把commit（）方法替换成 commitAllowingStateLoss()  

2，getSupportFragmentManager().popBackStackImmediate()方法在Activity的onSaveInstanceState()之后调用    
    解决方法：延迟处理在Activity的onResume()方法中执行该方法  

注：onSaveInstanceState方法执行的时间（与onRestoreInstanceState方法“不一定”是成对的被调用）：   
1、当用户按下HOME键时。  
2、长按HOME键，选择运行其他的程序时。  
3、按下电源按键（关闭屏幕显示）时。  
4、从activity A中启动一个新的activity时。  
5、屏幕方向切换时，例如从竖屏切换到横屏时。  

***

### 31,百度加固后，运行再小米2S等低版本手机会出现崩溃的问题。
	

现象：在小米2S中，一旦通过百度加固后，就会出现崩溃。
解决办法：通过逐步排查，发现只有是在Android Studio的项目中才会出现，通过二次排查，发现是在我们的gradle中配置了 android:debuggable = true 就会导致运行崩溃。但通过资料调研后，发现	android:debuggable = true与百度加密的崩溃并不会有直接关系，通过排除法再次分析，认为问题只可能出现在百度加固的这个过程中了。联系百度技术人员后，百度人员成功复现，并给出的解释为：

在mi2s上失败的原因，是因为mi2s集成了Lbe，lbe会在应用启动的时候注入应用进程，它的行为和百度加固的逻辑有冲突
Lbe要获取你们dex里的类，加固过后，他获取的时候，你们的dex里的类还没有被壳加载起来
之前lbe的问题我们联系了他们，他们不维护了，只能我们做兼容。

通过一波多折的多次迭代过后，测试通过。最终确定问题为百度加固过程中的不兼容性导致小米2S的手机崩溃。后续如果遇到此类问题，首先需要调试我们程序中的debuggable;确认不是程序问题后，及时沟通第三方人员。

***

### 30,Android6+系统，变声录音异常的解决办法：

1、权限改变

Android 6.0以前安装应用时就会弹出权限对话框给用户，而Android6.0以后再安装或打开应用时并不会弹出权限对话框，而是在你使用到当前功能（这里指的是录音功能时才会弹出）

2、系统不再支持8bit的编码率

上面已经说了，再低质量语音传输时8bit已经够了，再我们6.0以前使用8bit编码率对大部分的手机录音已经足矣，这里需要解释一下编码率到底是啥：

要算一个PCM音频流的码率是一件很轻松的事情，采样率值×采样大小值×声道数bps。一个采样率为44.1KHz，采样大小为16bit，双声道的PCM编码的WAV文件，它的数据速率则为 44.1K×16×2 =1411.2 Kbps。我们常说128K的MP3，对应的WAV的参数，就是这个1411.2 Kbps，这个参数也被称为数据带宽，它和ADSL中的带宽是一个概念。将码率除以8,就可以得到这个WAV的数据速率，即176.4KB/s。这表示存储一秒钟采样率为44.1KHz，采样大小为16bit，双声道的PCM编码的音频信号，需要176.4KB的空间，1分钟则约为10.34M，这对大部分用户是不可接受的。

所以有很多人为了再带宽上优化，增加采样率肯定是不可取的，所以就把16bit改成8bit，而对Andorid 6.0以前的影响并不会很大，但是在6.0以后，你再使用8bit就会出现异常了，这点一定要注意。

具体内容见：[android AudioRecord介绍与Android 6.0后的改变](http://blog.csdn.net/zhanggang740/article/details/51613983 "Link")   


***
### 29， Android 5月份细节点总结
1. 一个View，如果既设了padding，又设了paddingTop，那么只有padding生效，paddingTop是无效的。

2. 在开发下载功能的时候，使用Service和DB是用来在activity死掉后，管理和记录下载状态。

3. 可以把ListView的adapter设置为null，这样就只显示ListView的headView，4.0以下也是没有问题的。

4. 要注意这样的问题：ListView的adapter是通过一个list关联其item的，如果子线程会动态修改这个list（即子线程和adapter引用同一个对象，子线程会修改这个对象的值），在滑动ListView的时候就会有异常抛出。

5. 要十分注意数据库降版本的情况。

6. ListView如果布局高度不确定的时候，会计算其或其父控件的高度，所以会造成其getView方法被重复调用的情况。

7. If an activity is paused or stopped, the system can drop it from memory either by asking it to finish (calling its finish() method), or simply killing its process. 

8. 如果ListView没有HeaderView或者FooterView的时候，与ListView相关联的Adapter就是传进来的参数Adapter。如果有，则原来的Adapter将被包装成HeaderViewListAdapter，通过getWrappedAdapter()方法可以获取原来的Adapter。

9.尽量少使用setBackGround()方法设置背景，由于版本问题会引起运行错误;在activity中通过getWindow().setBackgroundDrawable(null);可以减少一个层级。（getWindow().setBackgroundDrawable()还有另外一个用法就是输入法弹下去时背景为黑色，可以通过这个来改为想要的颜色）。

10.如何让EditText不自动获取焦点？在EditText的父Layout中,加入下面的两个属性即可 : android:focusable="true"，android:focusableInTouchMode="true"。    

###  28. java float类型比较细节 ：
举个例子    27.2 == 272/10.0;   求输出结果 ;
一般人第一眼看到肯定觉得很简单 , 返回就是true嘛 ;    当告知你答案是false ,你依然不解,急着在编译器上实践，结果发现真的是false ,为什么呢？

答案是 ： java中直接声明 27.2 默认是为double类型 ; 而272/10.0 返回时一个float类型 ;  double == float 自然返回false ; 
可换成这个写法  27.2f == 272/10.0 ; 直接声明一个float类型 ;    

### 27.Android-G610手机出现的奇怪适配问题
机名：G610
Androd版本：4.2.2
主屏分辨率：960x540像素

问题：在此手机上出现适配问题如下图：
![pic_01](https://github.com/JackWaiting/pitfalls-android/blob/master/images_auto.png)

出现的原因：目前只能是猜测，由于手机和分辨率原因，导致下面的布局被拥挤，或某属性在此手机内部支持的可能性。如果大家有接触过类似的问题，欢迎指正。

最终的解决办法：更换其他布局，使用适配性更高的写法。
***


### 26，RadioGroup调用check(id)方法时，onCheckedChanged(RadioGroup group, int checkedId)方法被执行多

多次调用经常会干扰到程序的正常逻辑，导致出现奇怪的问题。最初我会放弃RadioGroup的onCheckedChanged()的监听，而改用它的onClick()事件，但是onClick()又会存在多次点击的问题，依旧不是比较理想的解法。  
要想让它只回调一次而不是多次，正确的做法应该是：RadioButton.setChecked(true); [Link](http://stackoverflow.com/questions/10263778/radiogroup-calls-oncheckchanged-three-times "Link")   


### 25，大图片裁剪终极解决方案  
APP在Android手机上实现拍照截图这一功能，虽然看起来非常简单，但是网上大多只是Demo水准，用在实际项目中问题漏洞百出，经常导致截取照片时程序异常的BUG。   
所幸在Google上搜到了一个不错的博客，才完美解决了这个问题。URL：[Android大图片裁剪终极解决方案](http://my.oschina.net/ryanhoo/blog/86842?fromerr=nYvxpdVH)   
总结一下最关键的原因，**截大图用URI，小图用Bitmap**，网上的Demo几乎都是用的Bitmap，而且并不提及如何使用URI。我们知道，现在的Android智能手机的摄像头都是几百万的像素，拍出来的图片都是非常大的。因此我们截图无论大图小图都要统一使用Uri进行操作。
关键代码： 

	private void cropImageUri(Uri uri, int outputX, int outputY, int requestCode){
	    Intent intent = new Intent("com.android.camera.action.CROP");
	    intent.setDataAndType(uri, "image/*");
	    intent.putExtra("crop", "true");
	    intent.putExtra("aspectX", 2);
	    intent.putExtra("aspectY", 1);
	    intent.putExtra("outputX", outputX);
	    intent.putExtra("outputY", outputY);
	    intent.putExtra("scale", true);
	    intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
	    intent.putExtra("return-data", false);
	    intent.putExtra("outputFormat", Bitmap.CompressFormat.JPEG.toString());
	    intent.putExtra("noFaceDetection", true); // no face detection
	    startActivityForResult(intent, requestCode);
	}

***

###  24.总结如何快速、高效、无错误的修改应用的包名
android如果想修改包名，如果牵扯到Manifest或者自定义控件带命名空间的。总会出现一些错误，比如，包名错乱、包名缺少、控件和控件交叉在一起。其实是可以避免这样错误的，总结如下：

**eclipse:**	
1.命名空间的书写问题		
例如：xmlns:myxmlns="http://schemas.android.com/apk/res/包名"，xmlns:app="http://schemas.android.com/apk/res-auto"第一个跟包名有很大关系，如果用第一个每次修改包名后，你要对应的xml里修改命名空间的包名。所以，不建议用第一种。	
2.xml结束符	
修改包名跟layout里面的控件。例如，		
<com.xxx.xxx.xxx.imageview></com.xxx.xxx.xxx.imageview>这样的方式很容易造成错误，建议用<com.xxx.xxx.xxx.imageview/>。这样更不容易出现错误。		
3.manifest文件最好复制出来，修改好包名再复制进去，然后进行修改。这样更好修改。如果直接不复制出来，你的service、receiver里面很容易出错。	
4.如果按照上面修改，在.java文件里也会出现错误，这个重新导下包就好了。		
**android studio:**		
android studio很好的解决了这个问题。	
修改好之后，重启启动下studio，在build.gradle里找到application id修改就好了。	

建议使用studio，eclipse修改不方便。很容易出现问题。	
***

###  23.布局优化
根据Android源码的分析(具体见文章)，RelativeLayout将对所有的子View进行两次measure，而LinearLayout在使用weight属性进行布局时也会对子View进行两次measure，如果他们位于整个View树的顶端时并可能进行多层的嵌套时，位于底层的View将会进行大量的measure操作，大大降低程序性能。因此，应尽量将RelativeLayout和LinearLayout置于View树的底层，并减少嵌套。  
配合`<include>`标签(布局重用)、`<merge>`标签(减少层级)、Viewstub(按需加载)。总之，关于布局优化，原则上尽量减少布局文件的层级。
***
###  22.Activity异常导致的资源回收
在编写代码时将数据放入文件中而不放static变量中，如登录的用户信息放在Sharepreference中，每次直接从其中取值。如果实在需要将数据放入static变量中，如模块信息，需要在activity的onSaveInstanceState执行保存。这样可以保证在执行内存清理后打开应用还能使用onRestoreInstanceState将数据找回。(OnRestoreInstanceState一旦被调用，其参数是一定有值的)。

***
###  21.For语句的使用
### #(1)不要在for的第二个条件中调用任何方法.
如：```for (int j = 0; j < element.length; j++)
改为for(int j = 0 , k = element.length; j < k ; j++) ```
减少执行element.length的次数。
### #(2)增强for循环.
能够用于实现了iterable接口的集合类及数组中。在集合类中，迭代器让接口调用hasNext()和next()方法。在ArrayList中，手写的计数循环迭代要快3倍(无论有没有JIT)，但其他集合类中，改进的for循环语法和迭代器具有相同的效率。    
***
###  20.Android 6.0系统注意事项(硬件设备)
根据Android官方文档：Android 6.0设备通过蓝牙和Wi-Fi扫描访问外部硬件设备时，你的应用需要添加ACCESS_FINE_LOCATION或者ACCESS_COARSE_LOCATION权限。    

###  19.radiobutton setChecked(false)后，再setChecked(true)无效，只有先选择其它的radiobutton才会有效。
原因：只设置了radiobutton的属性，并没有设置radiogroup的属性，所以对于radiogroup来说，它并不知道你的radiobutton已经设置成了false
<br>解决：调用radioGroup.clearCheck() 来清空选择

###  18.Fragment栈管理。加入进栈，清空栈。
        for (int i=0;i<getActivity().getSupportFragmentManager().getBackStackEntryCount();i++)
               getActivity().getSupportFragmentManager().popBackStack();	// 回退栈
         fragManager.beginTransaction()
                 .replace(layoutId, fragment)
                 .setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN)
                 // .addToBackStack(null)	// 是否将改动作添加到回退栈
                   .commit();

###  17.华为手机Intent不能传序列化的Object对象

Service没有在清单文件中配置，在开启该Service时，程序不会报错。

###  16.Fragment在退出后getActivity()方法返回为空，如在Handler中延迟执行一个事件，在触发之前退出改Fragment，触发时会出错。

###  15.点击事件变色的Shape
主要是android:state_focused="true" android:state_pressed="true"这两个属性，具体参考：[http://blog.csdn.net/u010134293/article/details/50161967](http://blog.csdn.net/u010134293/article/details/50161967 "自定义Drawable（文字按钮点击效果设置）")

###  14.APP界面图片显示错位或混乱，而资源文件的相关引用确定没有错误
问题：由于R文件生成错误导致
<br>解决：在发布项目之前**Clean Project**

###  13.for循环，ConcurrentModificationException异常。（并发修改）
	// 错误写法
	for (String item : set) {
		if ("bbbbbbb".equals(item)) {
    		set.remove(item);
		}
	}
	// 正确写法
	Iterator iterator = set.iterator();
	String item;
	while(iterator.hasNext()){
		item = (String) iterator.next();
		if ("bbbbbbb".equals(item)) {
			iterator.remove();
		}
	}

###  12.JNI加载so文件时，报java.lang.UnsatisfiedLinkError: dalvik.system.PathClassLoader错误，但在armeabi下有这个文件，并且文件名正确。
可能原因，还有其它的文件夹，如armeabi-v7a、x86、mips等文件夹，但是该文件夹下没有对应的so文件，可以通过删除其它文件来解决BUG。
<br>参考网址：[http://www.it165.net/embed/html/201410/2707.html](http://www.it165.net/embed/html/201410/2707.html "Androidndk开发打包时我们应该如何注意平台的兼容（x86,arm,arm-v7a）")

###  11.Apk安装错误：Installation error: INSTALL_FAILED_VERSION_DOWNGRADE
是因为 androidversionCode 的原因，我们手机里面的APP 的 versionCode 高于将要安装的 APP!
<br>将 androidversionCode 的版本号改成一个更高的版本。


### 10.关于自定义控件小米2s的坑总结
在自定义控件的时候有两个方法特别重要，第一个是onMeasure,第二个是onSizeChanged。onMeasure做计算屏幕的工作，但是小米2s，却在这里做了更多的处理。在切换到其他屏幕的时候，会多次执行onMeasure，切换回原来界面也会多次执行onMeasure。如果你在这里处理了逻辑问题，很可能会出现很多问题。目前可以把逻辑写入到onSizeChanged里面去。他会在第一次进入界面的时候调用。还有一种就是屏幕发生变化的时候进行调用，比如华为p6，它下面会多出一块操作区域。

### 9.关于gradle的使用
1、文件开头apply plugin是最新gradle版本的写法，以前的写法是apply plugin: ‘android’, 这里大家注意一下。 
2、buildToolsVersion这个需要你本地安装该版本才行，很多人导入新的第三方库，失败的原因之一是build version的版本不对，这个可以手动更改成你本地已有的版本或者打开 SDK Manager 去下载对应版本。 
3、applicationId代表应用的包名，也是最新的写法，这里就不在多说了。 
4、android 5.0开始默认安装jdk1.7才能编译，但是由于mac系统自带jdk的版本是1.6，所以需要手动下载jdk1.7并配置下，具体可以见我这篇博客Mac下安装和管理Java 
5、minifyEnabled也是最新的语法，很早之前是runProguard,这个也需要更新下。 
6、proguardFiles这部分有两段，前一部分代表系统默认的android程序的混淆文件，该文件已经包含了基本的混淆声明，免去了我们很多事，这个文件的目录在 sdk目录/tools/proguard/proguard-android.txt , 后一部分是我们项目里的自定义的混淆文件，目录就在 app/proguard-rules.txt , 如果你用Studio 1.0创建的新项目默认生成的文件名是 proguard-rules.pro , 这个名字没关系，在这个文件里你可以声明一些第三方依赖的一些混淆规则，由于是开源项目，SnailBulb_Basic_Android里并未进行混淆，具体混淆的语法也不是本篇博客讨论的范围。最终混淆的结果是这两部分文件共同作用的。 
具体参考：[http://blog.csdn.net/zhanggang740/article/details/49907745](http://blog.csdn.net/zhanggang740/article/details/49907745 "Gradle基础--认识Gradle")

### 8.关于小米闹钟弹框的坑总结
这个问题是之前做音箱类应用的时候遇到的，功能就是在应用未杀死的情况下，闹钟响时能弹出提示框。后来发现其他的手机都可以弹出，唯独是小米不行。原因既然是小米把系统的悬浮窗给禁掉了，只有用户手动开打这个权限后才能弹，大家可以注意下这个问题。

### 7.关于内存溢出的总结
最近已经写了一篇博客对这块进行了总结。
具体参考：[http://blog.csdn.net/zhanggang740/article/details/50393466](http://blog.csdn.net/zhanggang740/article/details/50393466 "关于内存溢出的总结")

### 6.关于在tf卡下接听电话的坑总结
在tf卡下有电话进来会自动切换到a2dp模式，在a2dp模式下不用处理tf的音乐，因为a2dp下调用卡音乐的方法会在来电时自动接听，去电时会自动挂断。

### 5.解决客户反馈打开应用就闪退的隐形坑
ListView嵌套GridView计算高度setGridViewHeightBasedOnChildren时，getView会计算到空值的情况，在这种情况下一定不要在convertView为null的情况下去调用hodler中刷新一些UI值，否则在某些手机下会出现空指针的情况，此时，如果调用View.setBackground方法会引起 NoSuchMethodError错误，这时我们一定要对这些手机版本进行控制，通过官方API提供的@TargetApi(Build.VERSION_CODES.JELLY_BEAN)进行控制即可，保证运行低版本时不会出现NoSuchMethodError错误。
如下图：
![pic_01](https://github.com/JackWaiting/pitfalls-android/blob/master/pitfalls.png)


### 4.不要在Android的Application对象中缓存数据
在我们App中的很多地方都需要使用到数据信息，它可能是一个session token，一次费时计算的结果等等，通常为了避免Activity之间传递数据的开销，会将这些数据通过持久化来存储。

有人建议将这些数据放在Application对象中方便所有的Activity访问，这个解决方案简单、优雅并且是……完全错误的。

你如果你将数据缓存到Application对象中，如何你并未对这个值进行初始化，那么有可能你的程序最终会由于一个NullPointerException异常而崩溃掉。如果你已经对他进行初始化，很有可能会出现在这个值快速更新的情况下，他会变成你初始化过后的值。
#### 为什么会这样？
在上面这个例子中，程序之所以会崩溃掉是因为恢复之后APP的Application对象是全新的，所以缓存在Application中的用户名成员变量为空值，在程序调用String的toUpperCase()方法时由于NullPointerException而崩溃掉。

导致这个问题的主要原因是：Application对象并不是始终在内存中的，它有可能会由于系统内存不足而被杀掉。但Android在你恢复这个应用时并不是重新开始启动这个应用，它会创建一个新的Application对象并且启动上次用户离开时的activity以造成这个app从来没有被kill掉得假象。

我们以为可以通过Application来缓存数据，却没想到恢复APP时直接跑了B Activity而不是先启动A Activity，最终导致的结果是程序意外的崩溃掉了。

#### 有哪些替代方法可用呢？
1、对于数据缓存问题我也没有比较好的办法，但你可以按照下面其中一种方式来处理：

2、通过Intent在Activity之间来传递数据（但是请别传递大量数据，这有可能导致程序异常或者ANR）；

3、使用官方推荐的方法中的一种将数据持久化，存储在磁盘中；

4、在使用数据和句柄的时候做空值检测；


### 3.四月Android开发细节点总结
1. 使用ListView时如果用到removeHeaderView，一定要确定ListView已经使用了setAdapter方法，不然会报NullPointException，addFooterView必须在setAdapter之前才会生效。

2. 使用ListView的时候，布局尽量使用fill_parent或者写死，如果使用wrap_content，它初始化的时候需要测量，会不断调用adapter的getView方法。

3. 使用ListView时如果要隐藏HeaderView，可以通过removeHeaderView来实现，也可以把headerView设为gone，然后headerView.setPadding(-headerView.height）来实现。

4. 使用ListView时尽量不使用onItemClick和onItemLongClick，而在adapter的getView中使用onClick和onLongClick。

5. Activity保存状态信息是应该在onPause时做，而不是onStop时做，以为可能因为内存紧张，可能不会调用onStop方法就已经被回收。

6. 在自定义一个UI控件的时候，一定要提供一个具有两个参数类型分别为Context和AttributeSet的构造函数，否则的话，该自定义控件就不可以在UI布局文件中使用。
 
7. 在定义Dialog，调用其dismiss和show方法的时候，一定要注意判断调用该Dialog的activity是不是已经为空或者已经finish了。

8. 单例中如果hold有context，一定要保证这个context是ApplicationContext，因为如果是Activity的context，会影响这个activity的回收。

9. 用SparseArray<E>代替HashMap能提高性能。

10. 当使用.9图做为一个view的background，如果在代码中动态修改了它的background，那么，这个view原先设置的padding将会失效。应先保存去padding值，然后等动态设置完background后再通过setPadding设置padding值。

### 2.总结Testin中出现的BUG问题
1、对问题进行setBackgrond会导致低版本出现java.lang.NoSuchMethodError问题。
解决此类问题的版本之前已经提示过，但是好像无法彻底解决，因此建议大家在项目中不要直接再去使用setBackgrond，因此带来的闪退是用户无法接受的，建议使用setBackgroundDrawablue和setbackgroundResource代替，以此来设备低版本出现的闪退问题。

2、ImageLoader导致出现的运行异常问题
目前对此问题的解决方法是在调用displayImage时进行try{}catch{}进行捕获，至少保证程序不会闪退。

3、Fragment中调用getResource导致的空指针异常
通过分析，出现此异常的原因可能是android生命周期引起的非正常情况，在某些手机上一旦出现Activity还未加载就获取getResource便会导致此类问题出现，解决的办法是延长此Fragment的生命周期，调用ApplicationContext();

4、在加载Log日志的时候，出现的空指针问题
在我们的程序中，有时候会去打印一些集合或者实例化对象的某些属性，这些属性在某些情况下不做非空判断是会导致空指针出现的，一般我们会忽略输出Log日志的忘掉非空判断，这里提醒大家，平时一定要注意。

### 1.Android 上传应用商店时出现ERROR getting 错误
最近出现一个bug，是上传应用商店的时候，部分应用商店会调用aapt工具获取apk信息，在获取信息时会出现错误。

这个错误并不长出现，只有一些国外的解决文章，还是花了一些时间才解决，这里记录一下了，如果少年们出现了类似的问题也可是试一下下面的解决方案。

首先我们如果出现上传问题以后可以使用aapt工具检测一下，工具在 sdk build-tools 文件夹下，用 cmd 的方式打开。
命令是 aapt dump badging xx.apk xx为应用名称，意思是获取apk相关信息。当程序出现问题就会出现和应用商店相同的提示信息。
ERROR getting 'android:name' attribute: attribute is not a string value
在我接手的项目里面出现这个问题的原因是，AndroidManiFest 中 activity 的 android:name= 用了@string的模式，这种相关的使用方式导致 aapt 无法识别。
修改方法就是把 @string 中的字符串复制到 android:name 中，然后使用 aapt 工具跑一下就可以解决问题了。如果重新打包的应用上传的应用商店时还出现错误提示，可以尝试刷新页面。

网上有国外的解决方案是吧 AndroidMainFest中所有 @string 都是用硬编码的方式写到文件里，这里其实并不需要的，只要没有提示 ERROR getting 'android:label' attribute: attribute is not a string value 或者是其他的类似提示，都只要修改 activity 里 android:name 就可以了。


