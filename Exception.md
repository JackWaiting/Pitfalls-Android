# Exception
在日常开发过程中，我们经常会遇到各种 Exception，导致这些异常的背后原因各种各样，在这里我们主要是整理、记录和归纳，在实际的项目开发过程中所遇到的常见异常的具体情况和分析。

## 6.Error

### 6.1 Android 5.0以下出现 java.lang.NoClassDefFoundError:

#### 问题描述：

随着Android版本的推移，网络上已经出现了越来越多的第三方库，在我们开发项目的过程中，由于大量的引用第三方库，在Android 5.0以下的手机运行就闪退，甚至有的机型无法安装，例如引用了okhttp,retrofit,环信，融云等都有可能会发现此异常。如果你同样也出现在5.0以上可运行，5.0以下运行崩溃，可用此方法解决。

#### 原因分析：

出现这个问题的主要原因是：方法数超 65536 限制。
由于实际开发当中的需求不断变更,开源框架越来越多，大多都用第三方 SDK，导致方法数很容易超出   65536 限制。出现错误 Java.lang.NoClassDefFoundError。

例如:

java.lang.NoClassDefFoundError:NoClassDefFoundError: okhttp3.OkHttpClient$Builder

测试6.0手机没问题，4.4上面就有问题了。导致出现以上错误崩溃。

当我查看源码并用 debug 调试后，发现此类并没有什么问题。

这个错误是 Android 应用的方法总数限制造成的。Android 平台的 Java 虚拟机 Dalvik 在执行 DEX 格式的 Java 应用程序时，使用原生类型 short 来索引 DEX 文件中的方法。这意味着单个 DEX 文件可被引用的方法总数被限制为 65536。通常 APK 包含一个 classes.dex 文件，因此 Android 应用的方法总数不能超过这个数量，这包括 Android 框架、类库和你自己开发的代码。而 Android 5.0 和更高版本使用名为 ART 的运行时，它原生支持从 APK 文件加载多个 DEX 文件。在应用安装时，它会执行预编译，扫描 classes(..N).dex 文件然后将其编译成单个 .oat 文件用于执行. 通熟的讲，就是分包。

#### 解决方法：

使用自定义的 Application 继承 MultiDexApplication 这个类，或者重写  Application 的方法 attachBaseContext()，并调用MultiDex.install()；

	@Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        MultiDex.install(this);
    }

使用以上方法即可解决，如果无法编译请将你的gradle版本配置如下：

	compileSdkVersion 23以上

    buildToolsVersion "23.0.0以上"

	defaultConfig {

          minSdkVersion 15

          targetSdkVersion 22

          // Enabling multidex support. 开关

              multiDexEnabled true

	}

## 5.RuntimeException

### 5.1: java.lang.RuntimeException: Parcelable encountered IOException writing serializable

#### 问题描述：
Android 中的 Activity 传递数据时，为了方便往往将很多数据封装成对象，然后将整个对象传递过去。传对象的时候有两种情况，一种是实现 Parcelable 接口，一种是实现 Serializable 接口。
可以用 bundle.putSerializable（Key，Object） 传递数据或者直接用 intent putExtrr（Key，Object） 传递数据。但当我们调用 bundle.putSerializable（Key，Object）时如抛出此异常可用以下方法解决。

#### 解决方法：

1、抛出 Java.io.NotSerializableException 异常

抛出这个异常是因为你的对象没有实现 Serializable 接口，只要实现该接口就好了。

2、抛出 java.lang.RuntimeException 异常

抛出这个异常是因为传递的对象里面的对象也要实现 Serializable 接口。

## 4. IllegalAccessException

## 3. IllegalStateException

## 2. Transformexception

## 1. RemoteServiceException


