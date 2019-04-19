[TOP](../../README.md)　>　Migrating to the latest version

---

# Migrating to the latest version

Instructions for how to migrate from previous versions of the F.O.X SDK

## 1. SDK Reinstallation

**[For Eclipse]**

1. Delete any previous versions of FOX_Android_SDK_{VERSION}.jar files in your project build
2. Add the [Latest SDK](https://github.com/forceoperationx/public-fox-android-sdk/releases) to your project's list of jar files
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
    implementation 'co.cyberz.fox:track-core:{VERSION}'
```

3.&nbsp;&nbsp;Execute the Gradle Build

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
* If you haven't done so already please review and complete our section on[De-duplication feature enhancement with external storage permission(Optional)](../external_storage/en/)

※ Make sure you test for any bugs/abnormalities as well as measurement tests after updating the SDK.


## 3. Implementation changes from previous versions (before 4.0.0)

### 3.1 Migration Warnings

* Make sure you delete any previous versions before integrating a new version.
* Make sure your project doesn't contain any previous versions. Otherwise, a build error will occur.


### 3.2 API Changes by Feature

|Feature| `〜 3.3.0` and below | `4.0.0 〜` and above|
|:---:|:---|:---|
|[Activation](../../README.md#activate_sdk_into_app)|**AndroidManifest.xml**<br>&lt;meta-data&gt;<br>・APPADFORCE_APP_ID<br>・APPADFORCE_CRYPTO_SALT<br>・ANALYTICS_APP_KEY|[FoxConfig](../sdk_api/README.md#foxconfig) config = <br>new [FoxConfig](../sdk_api/README.md#foxconfig)("*APPADFORCE_APP_ID*", "ANALYTICS_APP_KEY", "*APPADFORCE_CRYPTO_SALT*");<br>config.activate();|
|[Install Tracking](../../README.md#tracking_install)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("default");|[Fox](../sdk_api/README.md#fox).trackInstall();|
|[Install Tracking<br>(w/ options)](../track_install/README.md#track_install_optional)|AdManager ad = new AdManager( Context );<br>ad.sendConversion("*https://redirectSite.com*", "USER_0001");|[FoxTrackOption](../sdk_api/README.md#foxtrackoption) opt = new [FoxTrackOption](../sdk_api/README.md#foxtrackoption)();<br>opt.addRedirectUrl("*https://redirectSite.com*");<br>opt.addBuid("USER_0001")<br>[Fox](../sdk_api/README.md#fox).trackInstall(opt);|
|[External Browser Event Tracking](../track_events/README.md#track_by_browser)|AdManager ad = new AdManager(this);<br>LtvManager ltv = new LtvManager(ad);<br>ltv.ltvOpenBrowser("*http://yourhost.com/*");|[Fox](../sdk_api/README.md#fox).trackEventByBrowser("*http://yourhost.com/*");|
|[WebView Event Tracking](../track_events/README.md#track_by_webview)|AdManager ad = new AdManager(*Context*);<br>LtvManager ltv = new LtvManager(ad);<br>WebView mWebView = (WebView) findViewById(R.id.webview);<br>ltv.setLtvCookie(mWebView);<br>webView.loadUrl("*http://yourhost.com/*");|WebView webView = (WebView) findViewById(R.id.webview);<br>[Fox](../sdk_api/README.md#fox).trackEventByWebView(webView);<br>webView.loadUrl("*http://yourhost.com/*");|
|[Re-engagement measurement](../../README.md#tracking_reengagement)|AdManager ad = new AdManager( Context );<br>ad.sendReengagementConversion(Intent);|[Fox](../sdk_api/README.md#fox).trackDeeplinkLaunch(Intent);|
|[Session Measurement](../../README.md#tracking_session)|AnalyticsManager.sendStartSession( Context );|[Fox](../sdk_api/README.md#fox).trackSession();|
|[Event Tracking<br>(In-App Purchases)](../../README.md#tracking_event_purchase)|// In-App Purchase measurement by LTV<br>AdManager ad = new AdManager( Context )<br>LtvManager ltv = new LtvManager( ad );<br>ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");<br>ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");<br>ltv.sendLtvConversion( *conversion point ID* );<br><br>// In-App purchase measurement by access analytics(`※1`)<br>AnalyticsManager.sendEvent(Context, "*EventName*", action, null, null, orderId, sku, itemName, 9.99, 1, "USD");|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*EventName*", *conversion point ID*);<br>event.price = 9.99;<br>event.currency = "USD";<br>event.quantity = 1;<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|
|[Event Tracking<br>(Tutorial Completion)](../../README.md#tracking_event_tutorial)|AnalyticsManager.sendEvent(Context, "*Tutorial Completion*", null, null, 1);|[FoxEvent](../sdk_api/README.md#foxevent) event = new [FoxEvent](../sdk_api/README.md#foxevent)("*Tutorial Completion*");<br>[Fox](../sdk_api/README.md#fox).trackEvent(event);|

> `※1` <strong>WARNING:</strong> When migrating from any version before 4.0.0, be sure not to change the `event name` used for measuring In-App purchases. If such a change occurs, any data collected before the migration will lose its integrity.

### 3.3 Additional Information

#### 3.3.1 Enabling multiple BroadcastReceivers

**Integrations before `〜 3.3.0`**

```xml
<!-- Set the receiver class to the F.O.X SDK's class -->
<receiver
    android:name="jp.appAdForce.android.InstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- Add any other receiver classes you might want the F.O.X SDK to call as meta-data -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver" />
```

**[Integrations after `4.0.0 〜`](../install_referrer/README.md)**

```xml
<!-- Set the receiver class to the F.O.X SDK's class -->
<receiver
    android:name="co.cyberz.fox.FoxInstallReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
</receiver>

<!-- Pipe the paths of any other receiver classes you might want the F.O.X SDK to call as meta-data -->
<meta-data
        android:name="APPADFORCE_FORWARD_RECEIVER"
        android:value="com.example.InstallReceiver1|com.example.InstallReceiver2|com.example.InstallReceiver3" />
```

#### 3.3.2 Proguard Settings

<br>**Integrations before `〜 3.3.0`**<br>
**[DELETE]**

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

<br>**Integrations after `4.0.0 〜`**<br>
**[ADD]**

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
### If you installed the SDK through Gradle, you may omit the following .jar file. ###
-libraryjars libs/AppAdForce.jar
```

---
[Return to Top](../../README.md)
