[TOP](../../README.md)　>　升级更新到最新版本

---

# 升级更新到最新版本

以前导入过旧版F.O.X SDK的APP中导入最新SDK时，请按照以下的顺序进行。

## 1. SDK的替换

**[Eclipse环境**

1. 如果项目中安装了旧版FOX_Android_SDK_{VERSION}.jar，将其删除。
2. 将[最新安定版(Latest release)SDK](https://github.com/forceoperationx/public-fox-android-sdk/releases) jar文件添加到项目的libs中。
3. Eclips［项目］→实行［clean］

**[AndroidStudio环境]**

1.&nbsp;&nbsp;删除旧版 *FOX_Android_SDK_{VERSION}.jar* 。APP项目`build.gradle`中的依赖关系(dependencies)里有以下记载内容的话，请删除。<br>

**[删除]**

```
    compile 'co.jp.cyberz.fox:sdk-android:{VERSION}'
```

2.&nbsp;&nbsp;在APP项目的`build.gradle`中追加下面的依赖关系(dependency)。


**[追加]**

```
    implementation 'co.cyberz.fox:track-core:{VERSION}'
```

3.&nbsp;&nbsp;执行Gradle Build

## 2. 更改设置

### 2.1 修改设定所需的参数

目前为止AndroidManifest.xml中不需要以下meta-data的设置，在Application继承Class的onCreate中做设置。

**[消除]**

```xml
<meta-data
    android:name="APPADFORCE_APP_ID"
    android:value="1234" />
<meta-data
    android:name="APPADFORCE_SERVER_URL"
    android:value="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" />
<meta-data
    android:name="APPADFORCE_CRYPTO_SALT"
    android:value="YYYYYYYYYYYYYYYYYYYYYYYYYY" />
<meta-data
    android:name="ANALYTICS_APP_KEY"
    android:value="ZZZZZZZZZZZZZZZZZZZZZZZZZZ" />
```

**[所需参数的设置位置]**

|所需参数|〜3.3.0|4.0.0〜|
|:---:|:---:|:---:|
|APPADFORCE_APP_ID|AndroidManifest.xml|Application継承Class内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|APPADFORCE_SERVER_URL|AndroidManifest.xml|不需要|
|APPADFORCE_CRYPTO_SALT|AndroidManifest.xml|Application継承Class内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|ANALYTICS_APP_KEY|AndroidManifest.xml|Application継承Class内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|

### 2.2 设置的重估

* 未实行「[（任意）导入Google Play Services来使用广告ID](../google_play_services//README.md)」的情况，请考虑该选项。
* 未实行「[（任意）使用外部储存优化排除重复](../external_storage/README.md)」的情况，请考虑该选项。

※ SDK 更新后，必须测试计测效果，并确认计测及APP运行没有问题。

## 3. 更新旧版本(4.0.0以下)的执行方法

### 3.1 升级过渡时的注意点

* 必须将旧版本从项目中删除后再进行导入。
* APP项目中请不要将SDK和旧版本(不足4.0.0版本)混淆。会发生编译错误。

### 3.2 不同计测的API适用表

|种别| `〜 3.3.0` 的实际安装 | `4.0.0 〜` 的实际安装|
|:---:|:---|:---|
|[激活](../../README.md#activate_sdk_into_app)|**AndroidManifest.xml**<br>&lt;meta-data&gt;<br>・APPADFORCE_APP_ID<br>・APPADFORCE_CRYPTO_SALT<br>・ANALYTICS_APP_KEY|[FoxConfig](../sdk_api/README.md#foxconfig) config = <br>new [FoxConfig](../sdk_api/README.md#foxconfig)("*APPADFORCE_APP_ID*", "*APPADFORCE_CRYPTO_SALT*", "ANALYTICS_APP_KEY");<br>config.activate();|
|[Install计测](../../README.md#tracking_install)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("default");|[Fox](../sdk_api/README.md#fox).trackInstall();|
|[Install计测<br>(带Option)](../track_install/README.md#track_install_optional)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("*https://redirectSite.com*", "USER_0001");|[FoxTrackOption](../sdk_api/README.md#foxtrackoption) opt = new [FoxTrackOption](../sdk_api/README.md#foxtrackoption)();<br>opt.addRedirectUrl("*https://redirectSite.com*");<br>opt.addBuid("USER_0001")<br>[Fox](../sdk_api/README.md#fox).trackInstall(opt);|
|[用外部浏览器进行事件计测](../track_events/README.md#track_by_browser)|AdManager ad = new AdManager(this);<br>LtvManager ltv = new LtvManager(ad);<br>ltv.ltvOpenBrowser("*http://yourhost.com/*");|[Fox](../sdk_api/README.md#fox).trackEventByBrowser("*http://yourhost.com/*");|
|[用APP内WebView进行事件计测](../track_events/README.md#track_by_webview)|AdManager ad = new AdManager(*Context*);<br>LtvManager ltv = new LtvManager(ad);<br>WebView mWebView = (WebView) findViewById(R.id.webview);<br>ltv.setLtvCookie(mWebView);<br>webView.loadUrl("*http://yourhost.com/*");|WebView webView = (WebView) findViewById(R.id.webview);<br>[Fox](../sdk_api/README.md#fox).trackEventByWebView(webView);<br>webView.loadUrl("*http://yourhost.com/*");|
|[流失唤回广告计测](../../README.md#tracking_reengagement)|AdManager ad = new AdManager( Context );<br>ad.sendReengagementConversion(Intent);|[Fox](../sdk_api/README.md#fox).trackDeeplinkLaunch(Intent);|
|[Session计测](../../README.md#tracking_session)|AnalyticsManager.sendStartSession( Context );|[Fox](../sdk_api/README.md#fox).trackSession();|
|[事件计测<br>(付费)](../../README.md#tracking_event_purchase)|// 通过LTV计测的付费计测<br>AdManager ad = new AdManager( Context )<br>LtvManager ltv = new LtvManager( ad );<br>ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");<br>ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");<br>ltv.sendLtvConversion( *成果地点ID* );<br><br>// 依靠流量分析的付费计测(`※1`)<br>AnalyticsManager.sendEvent(Context, "*事件名*", action, null, null, orderId, sku, itemName, 9.99, 1, "USD");|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*事件名*", *成果地点ID*);<br>event.price = 9.99;<br>event.currency = "USD";<br>event.quantity = 1;<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|
|[事件计测<br>(完成新手引导)](../../README.md#tracking_event_tutorial)|AnalyticsManager.sendEvent(Context, "*完成新手引导*", null, null, 1);|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*完成心都引导*");<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|

> ※1 请注意，迁移到4.0.0以上的版本时如果修改旧版本的`事件名`，将无法继续保留流量分析中计测到的数据。

### 3.3 其他

#### 3.3.1 BroadcastReceiver的多选

**[`〜 3.3.0` 的实际安装]**

```xml
<!-- Receiver类指定F.O.X SDK的Class -->
<receiver
    android:name="jp.appAdForce.android.InstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- 用 F.O.X SDK呼出其他Receiver类时信息记录为meta-data -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver" />
```

**[`4.0.0 〜` 的实际安装](../install_referrer/README.md)**

```xml
<!-- Receiver类指定F.O.X SDK的Class -->
<receiver
    android:name="co.cyberz.fox.FoxInstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- 希望用F.O.X SDK呼出其他Receiver类的路径时用|(竖线)隔开记录 -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver1|com.example.InstallReceiver2|com.example.InstallReceiver3" />
```

#### 3.3.2 设定Proguard

<br>**[〜 3.3.0 的实际安装]**<br>
**[删除]**

```
-keepattributes *Annotation*
-libraryjars libs/AppAdForce.jar
-keep interface jp.appAdForce.** { *; }
-keep class jp.appAdForce.** { *; }
-keep class jp.co.dimage.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn jp.appAdForce.android.**
-dontwarn jp.co.dimage.**
-dontwarn jp.co.cyberz.fox.**
-dontwarn com.adobe.fre.**
-dontwarn com.ansca.**
-dontwarn com.naef.jnlua.**
```

<br>**[4.0.0〜 的实际安装]**<br>
**[添加]**

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
# 通过Gradle安装SDK的情况，不需要指定下述jar文件。
-libraryjars libs/AppAdForce.jar
```

---
[Top](../../README.md)
