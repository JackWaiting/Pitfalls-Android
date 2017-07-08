# android-google-play-and-other-app-store-review-guidelines-tips

## 1.多渠道打包 360 被拒    
问题说明： 目前多渠道打包一般分有百度，360 等。但目前 360 和百度版本更新互不兼容，如果代码内部包含了非当前的版本更新代码就会被拒。  
问题原因： 百度、360 版本更新代码互不兼容。

解决方法： 目前多渠道打包还是不够完善的解决这个问题，目前的处理方式，就是打百度包去掉360更新，打 360 去掉百度更新。

## 2.Google Play Store 搜索不到上线的应用问题总结

* 原因：当用户在 Google Play 上搜索或浏览应用以下载时，会根据哪些应用与其设备兼容来过滤搜索结果。例如，如果应用需要摄像头，Google Play 不会在没有摄像头的设备上显示该应用。这种过滤帮助开发者管理其应用的分发，并且有助于确保为用户提供最佳的体验。

* 过滤规则 ： https://developer.android.com/google/play/filters.html?hl=zh-cn
需要硬件支持的权限也会默认进行设备兼容过滤；


* 如何去掉设备兼容过滤 ：
Nexus 7 没有电话功能 ，然而在应用的 Manifest 文件里声明了电话相关的权限，希望不支持电话功能的设备也能搜索该 APP
就在 Manifest 文件添加如下代码 ：

 <uses-feature android:name="android.hardware.telephony" android:required="false"/>

 注：通过显式声明某项功能并加入 android:required="false" 属性，可以在 Google Play 上有效停用所有针对指定功能的过滤。

更多功能声明 ： https://developer.android.com/guide/topics/manifest/uses-feature-element.html?hl=zh-cn#permissions-features
