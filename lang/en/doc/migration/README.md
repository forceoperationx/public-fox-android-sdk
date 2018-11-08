[TOP](../../README.md)　>　Migrating to the latest version

---

# Migrating to the latest version

Instructions for how to migrate from previous versions of the F.O.X SDK

## 1. SDK Reinstallation

**[For Eclipse]**

1. Delete any previous versions of FOX_Android_SDK_{VERSION}.jar files in your project build
2. Add the [Latest SDK](https://github.com/cyber-z/public-fox-android-sdk/releases) to your project's list of jar files
3. Clean your Eclipse project

**[For AndroidStudio]**

1.&nbsp;&nbsp;Delete any previous versions of *FOX_Android_SDK_{VERSION}.jar* files.

**[REMOVE]**

```
    compile 'co.jp.cyberz.fox:sdk-android:{VERSION}'
```

2.&nbsp;&nbsp;Then, add the following to your `build.gradle` dependency list.

**[ADD]**

```
    compile 'co.cyberz.fox:track-core:{VERSION}'
```

3.&nbsp;&nbsp;Executing the Gradle Build

## 2. Change Settings

### 2.1 New Parameter Settings

The following meta-data that was formerly set in your AndroidManifest is no longer necessary. Please set it inside your Application class' onCreate method.

**[DELETE]**

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

**[New Parameter Settings Overview]**

|Parameters|〜3.3.0|4.0.0〜|
|:---:|:---:|:---:|
|APPADFORCE_APP_ID|AndroidManifest.xml|Within your App class' onCreate method<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|APPADFORCE_SERVER_URL|AndroidManifest.xml|N/A|
|APPADFORCE_CRYPTO_SALT|AndroidManifest.xml|Within your App class' onCreate method<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|ANALYTICS_APP_KEY|AndroidManifest.xml|Within your App class' onCreate method<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|

### 2.2 Settings Review

* If you haven't done so already please review and complete our section on [Setting Up Google Play Services for advertisement ID acquisition(Optional)](../google_play_services/en/)
* If you haven't done so already please review and complete our section on[De-duplication feature enhancement (external storage permission(Optional)](../external_storage/en/)

※ Make sure you test for any bugs/abnormalities as well as measurement tests after updating the SDK.


## 3. Implementation changes from previous versions (before 4.0.0)

### 3.1 マイグレーション時の注意

* 必ず旧バージョンはプロジェクト内から削除した上で導入してください。
* アプリケーションのプロジェクトにSDKは旧バージョン(4.0.0未満)とを混在しないようにしてください。ビルドエラーが発生します。


### 3.2 計測別のAPI対応表

|種別| `〜 3.3.0` の実装 | `4.0.0 〜` の実装|
|:---:|:---|:---|
|[アクティベーション](../../README.md#activate_sdk_into_app)|**AndroidManifest.xml**<br>&lt;meta-data&gt;<br>・APPADFORCE_APP_ID<br>・APPADFORCE_CRYPTO_SALT<br>・ANALYTICS_APP_KEY|[FoxConfig](../sdk_api/README.md#foxconfig) config = <br>new [FoxConfig](../sdk_api/README.md#foxconfig)("*APPADFORCE_APP_ID*", "ANALYTICS_APP_KEY", "*APPADFORCE_CRYPTO_SALT*");<br>config.activate();|
|[インストール計測](../../README.md#tracking_install)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("default");|[Fox](../sdk_api/README.md#fox).trackInstall();|
|[インストール計測<br>(オプション付)](../track_install/README.md#track_install_optional)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("*https://redirectSite.com*", "USER_0001");|[FoxTrackOption](../sdk_api/README.md#foxtrackoption) opt = new [FoxTrackOption](../sdk_api/README.md#foxtrackoption)();<br>opt.addRedirectUrl("*https://redirectSite.com*");<br>opt.addBuid("USER_0001")<br>[Fox](../sdk_api/README.md#fox).trackInstall(opt);|
|[外部ブラウザでイベント計測](../track_events/README.md#track_by_browser)|AdManager ad = new AdManager(this);<br>LtvManager ltv = new LtvManager(ad);<br>ltv.ltvOpenBrowser("*http://yourhost.com/*");|[Fox](../sdk_api/README.md#fox).trackEventByBrowser("*http://yourhost.com/*");|
|[アプリ内WebViewでイベント計測](../track_events/README.md#track_by_webview)|AdManager ad = new AdManager(*Context*);<br>LtvManager ltv = new LtvManager(ad);<br>WebView mWebView = (WebView) findViewById(R.id.webview);<br>ltv.setLtvCookie(mWebView);<br>webView.loadUrl("*http://yourhost.com/*");|WebView webView = (WebView) findViewById(R.id.webview);<br>[Fox](../sdk_api/README.md#fox).trackEventByWebView(webView);<br>webView.loadUrl("*http://yourhost.com/*");|
|[リエンゲージメント計測](../../README.md#tracking_reengagement)|AdManager ad = new AdManager( Context );<br>ad.sendReengagementConversion(Intent);|[Fox](../sdk_api/README.md#fox).trackDeeplinkLaunch(Intent);|
|[セッション計測](../../README.md#tracking_session)|AnalyticsManager.sendStartSession( Context );|[Fox](../sdk_api/README.md#fox).trackSession();|
|[イベント計測<br>(課金)](../../README.md#tracking_event_purchase)|// LTV計測による課金計測<br>AdManager ad = new AdManager( Context )<br>LtvManager ltv = new LtvManager( ad );<br>ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");<br>ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");<br>ltv.sendLtvConversion( *成果地点ID* );<br><br>// アクセス解析による課金計測(`※1`)<br>AnalyticsManager.sendEvent(Context, "*イベント名*", action, null, null, orderId, sku, itemName, 9.99, 1, "USD");|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*イベント名*", *成果地点ID*);<br>event.price = 9.99;<br>event.currency = "USD";<br>event.quantity = 1;<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|
|[イベント計測<br>(チュートリアル完了)](../../README.md#tracking_event_tutorial)|AnalyticsManager.sendEvent(Context, "*チュートリアル完了*", null, null, 1);|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*チュートリアル完了*");<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|

> ※1 バージョン4.0.0以降にマイグレーションする際、これまで旧バージョンで指定していた`イベント名`を変更してしまうと、アクセス解析にて計測してきた集計データが引き継がれなくなりますのでご注意ください。

### 3.3 その他

#### 3.3.1 BroadcastReceiverの複数指定

**[`〜 3.3.0` の実装]**

```xml
<!-- レシーバークラスはF.O.X SDKのクラスを指定 -->
<receiver
    android:name="jp.appAdForce.android.InstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- F.O.X SDKから呼び出したい他のレシーバークラス情報をmeta-dataとして記述 -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver" />
```

**[`4.0.0 〜` の実装](../install_referrer/README.md)**

```xml
<!-- レシーバークラスはF.O.X SDKのクラスを指定します -->
<receiver
    android:name="co.cyberz.fox.FoxInstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- F.O.X SDKから呼び出したい他のレシーバークラスのパスを|(パイプ)区切りで記述します -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver1|com.example.InstallReceiver2|com.example.InstallReceiver3" />
```

#### 3.3.2 Proguardの設定

<br>**[〜 3.3.0 の実装]**<br>
**[削除]**

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

<br>**[4.0.0〜 の実装]**<br>
**[追加]**

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
# Gradle経由でSDKをインストールしている場合、下記jarファイルの指定は不要です。
-libraryjars libs/AppAdForce.jar
```

---
[トップ](../../README.md)
