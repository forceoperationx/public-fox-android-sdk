[TOP](../../README.md)　>　最新バージョンへのマイグレーションについて

---

# 最新バージョンへのマイグレーションについて

以前のF.O.X SDKが導入されたアプリに最新のSDKを導入する際に必要な手順は以下の通りです。

## 1. SDKの入れ替え

**[Eclipseの場合]**

1. 以前のバージョンの FOX_Android_SDK_{VERSION}.jarがプロジェクトに組み込まれていれば、それらを削除
2. [最新のSDK](https://github.com/cyber-z/public-fox-android-sdk/releases) jarファイルをプロジェクトのlibsに追加
3. Eclipse の「プロジェクト」→「クリーン」を実行

**[AndroidStudioの場合]**

1.&nbsp;&nbsp;以前のバージョンの *FOX_Android_SDK_{VERSION}.jar* を削除する。また、アプリプロジェクトの`build.gradle`のdependenciesに以下の指定が記述されていれば削除する。<br>

**[削除]**

```
    compile 'co.jp.cyberz.fox:sdk-android:{VERSION}'
```

2.&nbsp;&nbsp;アプリプロジェクトの`build.gradle`に以下のdependencyを追記する。

**[追加]**

```
    compile 'co.cyberz.fox:foxtrack-core:{VERSION}'
```

3.&nbsp;&nbsp;Gradle Buildを実行

## 2. 設定の変更

### 2.1 設定必須パラメータの変更

これまでAndroidManifest.xmlに記述していた以下のmeta-dataの設定箇所が不要となり、Application継承クラスのonCreate内での設定となります。

**[削除]**

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

**[必須パラメータの設定箇所]**

|必須パラメータ|〜3.3.0|4.0.0〜|
|:---:|:---:|:---:|
|APPADFORCE_APP_ID|AndroidManifest.xml|Application継承クラス内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|APPADFORCE_SERVER_URL|AndroidManifest.xml|不要|
|APPADFORCE_CRYPTO_SALT|AndroidManifest.xml|Application継承クラス内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|
|ANALYTICS_APP_KEY|AndroidManifest.xml|Application継承クラス内<br>[FoxConfig](../sdk_api/README.md#foxconfig).java|

### 2.2 設定の見直し

* 「[（オプション）広告IDを利用するためのGoogle Play Services SDKの導入](../google_play_services/ja/)」が未実施の場合には対応をご検討ください。
* 「[（オプション）外部ストレージを利用した重複排除設定](../external_storage/ja/)」が未実施の場合には対応をご検討ください。

※ SDK のアップデート後は、必ず効果測定テストを実施し、計測及びアプリケーションの動作に問題ないことを確認してください。


## 3. 旧バージョン(4.0.0未満)からの実装方法を更新

### 3.1 マイグレーション時の注意

* 必ず旧バージョンはプロジェクト内から削除した上で導入してください。
* アプリケーションのプロジェクトにSDKは旧バージョン(4.0.0未満)とを混在しないようにしてください。ビルドエラーが発生します。


### 3.2 計測別のAPI対応表

|種別| `〜 3.3.0` の実装 | `4.0.0 〜` の実装|
|:---:|:---|:---|
|[アクティベーション](../../README.md#activate_sdk_into_app)|**AndroidManifest.xml**<br>&lt;meta-data&gt;<br>・APPADFORCE_APP_ID<br>・APPADFORCE_CRYPTO_SALT<br>・ANALYTICS_APP_KEY|[FoxConfig](../sdk_api/README.md#foxconfig) config = <br>new [FoxConfig](../sdk_api/README.md#foxconfig)("*APPADFORCE_APP_ID*", "*APPADFORCE_CRYPTO_SALT*", "ANALYTICS_APP_KEY");<br>config.activate();|
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
