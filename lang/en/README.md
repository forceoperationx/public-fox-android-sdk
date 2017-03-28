# About the Force Operation X

Force Operation X (Hereinafter referred to as F.O.X) is total solution platform optimizing the ad effectiveness on Smartphone. Including downloading application and the calculation of user's actions, it is able to maximize the price-performance ratio of companies' promotion according to criteria of original effective measurements based on behavior characteristics of Smartphone users.

This documents instructs the integration procedures of F.O.X. SDK to maximize the ad effectiveness on Smartphone application.

## Table of contents

* **[1. Install SDK](#install_sdk)**
	* [SDK download](https://github.com/cyber-z/public_fox_android_sdk/releases)
	* [How to install to AndroidStudio project](./doc/integration/android_studio/README.md)
	* [How to install to Eclipse project](./doc/integration/eclipse/README.md)
* **[2. Settings](#setting_sdk)**
* **[3. Implement install tracking](#tracking_install)**
	* [The detail of sendConversion method](./doc/send_conversion/README.md)
* **[4. Implement LTV tracking](#tracking_ltv)**
	* [LTV tracking by using tag](./doc/ltv_browser/README.md)
* **[5. Implement access analytics](#tracking_analytics)**
	* [Event tracking by using access analytics](./doc/analytics_event/README.md)
	* [Purchase-event tracking by using access analytics](./doc/analytics_purchase/README.md)
* **[6. In case of using ProGuard](#use_proguard)**
* **[7. Integration test](#integration_test)**
	* [Integration test for measuring reengagement](./doc/reengagement_test/README.md)
* **[8. Implementation of other function](#other_function)**
	* [Implement opt out](./doc/optout/README.md)
	* [Setting of Google Play Services SDK for using Advertising ID](./doc/google_play_services/README.md)
	* [Setting of deduplication using external storage](./doc/external_storage/README.md)
	* [Improvement of duplicated installation determination accuracy using auto backup in Android M(6.0)](./doc/auto_backup/README.md)
* **[9. Trouble shooting](#trouble_shooting)**

## What is F.O.X SDK?

Installing F.O.X SDK into application realizes the following functions.

* **Install tracking**

It is able to measure the number of installation for each inflows of advertisement.

* **LTV measurement**

It measures Life Time Value for each advertisements of influx sources. Primary conversion points are membership registration, completion of tutorial, and billing. It is able to measure registration rate, billing rate, and the billing amount for each advertisement.

* **Access Analytics**

It measure comparison of Installation number of organic and non-organic, the number of the of activating application and unique users (DAU / MAU), user retention and so on.

<div id="install_sdk"></div>

## 1. Install SDK

Add this to your project's build.gradle file.

```
repositories {
    maven {
        url "https://github.com/cyber-z/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    compile 'co.jp.cyberz.fox:sdk-android:3.3.0'
}
```

If you are not using Gradle, download latest SDK from below link and add the `libs/FOX_Android_SDK_{VERSION}.jar` to the project's class path.

* [SDK download](https://github.com/cyber-z/public_fox_android_sdk/releases)

[Other]
* [How to install to AndroidStudio project](./doc/integration/android_studio/README.md)
* [How to install to Eclipse project](./doc/integration/eclipse/README.md)

> ※ If you integrate old version of F.O.X SDK, please check [Updating to latest version of F.O.X SDK](./doc/update/README.md).

<div id="setting_sdk"></div>

## 2. Settings

* **AndroidManifest.xml**

### Set the Required Permissions

The AndroidManifest.xml should include the following permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

`READ_EXTERNAL_STORAGE` and `WRITE_EXTERNAL_STORAGE` permission are optional.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Adding this permission enables to improve deduplication to install event.

Permission|Protection Level|Mandatory|Outline
:---|:---:|:---:|:---
INTERNET|Normal|Mandatory|It is necessary so that F.O.X SDK operates communication.
ACCESS_NETWORK_STATE|Normal|Mandatory|It is necessary so that F.O.X SDK confirm operates communication.
READ_EXTERNAL_STORAGE ※1|Dangerous|Optional|It is necessary to improve deduplication using storage.(※)
WRITE_EXTERNAL_STORAGE ※1|Dangerous|Optional|It is necessary to improve deduplication using storage.(※)

> ※ To use the function which is necessary to use the permission specified that ProtectionLevel is dangerous by Android M, it is necessary to get the permission from users. For more detail, check [Setting of deduplication using external storage](./doc/external_storage/README.md).

### Set the metadata

Add following <meta-data>tags to manifest file, within <application>tag.

```xml
<meta-data
	android:name="APPADFORCE_APP_ID"
	android:value="There will be a contact from the administrator of Force Operation X, so please type the value." />
<meta-data
	android:name="APPADFORCE_SERVER_URL"
	android:value="There will be a contact from the administrator of Force Operation X, so please type the value." />
<meta-data
	android:name="APPADFORCE_CRYPTO_SALT"
	android:value="There will be a contact from the administrator of Force Operation X, so please type the value." />
<meta-data
	android:name="ANALYTICS_APP_KEY"
	android:value="There will be a contact from the administrator of Force Operation X, so please type the value." />
```

Key and value for setting are following.

|Parameter|Mandatory|Outline|
|:------|:------|:------|
|APPADFORCE_APP_ID|Mandatory|There will be a contact from the administrator of Force Operation X, so please type the value.|
|APPADFORCE_SERVER_URL|Mandatory|There will be a contact from the administrator of Force Operation X, so please type the value.|
|APPADFORCE_CRYPTO_SALT|Mandatory|There will be a contact from the administrator of Force Operation X, so please type the value.|
|ANALYTICS_APP_KEY|Mandatory|There will be a contact from the administrator of Force Operation X, so please type the value.|


### Set the Install Referrer Broadcast Receiver
Add the following receiver to manifest file, within <application>tag.

```xml
<receiver android:name="jp.appAdForce.android.InstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

In the case that receiver class to "com.android.vending.INSTALL_REFERRER" is already defined, please refer to [Setting in the case of making multiple INSTALL_REFERRER receivers coexist](./doc/install_referrer/README.md)

### Set the URL Scheme

Activities that are meant to be opened should have the below intent filter on the activity definitions in the manifest file.

```xml
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
	<data android:scheme="your.app.scheme" />
</intent-filter>
```

> Depending on the model, capitalization of the URL scheme may not be distinguished, so it may not be possible to successfully transition the URL scheme. Please set the URL scheme using all lower case alphanumeric characters.

### Setting of Google Play Services SDK for using Advertising ID

To use the advertisement ID, Google Play Services SDK needs to be incorporated. In the case of not incorporating Google Play Services SDK into application, please follow the procedures in next site and conduct implementation.
Please refer to [The implementation of Google Play Services SDK to use advertisement ID](./doc/google_play_services/README.md) for details of implementation.

### Setting sample for AndroidManifest.xml

[Sample for AndroidManifest.xml](./doc/config_android_manifest/AndroidManifest.xml)


<div id="tracking_install"></div>

## 3. Implement install tracking

By implementing install tracking , it is able to start the effectiveness measurements of advertisement. Please implement in a order following.

Add `sendConversion` to onCreate() of Activity that is called upon when launching the application.

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);

	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}
```

Input the string "default" to the argument of sendConversion as above.

* [The detail of sendConversion method](./doc/send_conversion/README.md)

<div id="reengagement_tracking"></div>
### Measuring re-engagement

In order to measure re-engagement, add `sendDeeplinkConversion` to onResume() of all activity where the URL Scheme is set.

![F.O.X](http://img.shields.io/badge/Version-3.5.0%20+-blue.svg?style=flat)

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendDeeplinkConversion(getIntent());
}
```

> Up to SDK version 3.4.0, we had provided `sendReengagementConversion` for re-engagement measurement. But as of version 3.5.0, please implement `sendDeeplinkConversion` with [deferred deeplink](./doc/deferred_deeplink/README.md) support.


![F.O.X](http://img.shields.io/badge/Version-〜%203.4.0-blue.svg?style=flat)

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendReengagementConversion(getIntent());
}
```

> Please note that the `sendReengagementConversion` is Deprecated since SDK version 3.5.0.

If the launchMode of the Activity opened by the URL scheme is "singleTask" or "singleInstance", override the `onNewIntent` to receive parameters of URL scheme and call `setIntent` as follows.


```java
@Override
protected void onNewIntent(Intent intent)
{
	super.onNewIntent(intent);
	setIntent(intent);
}
```

> For measurement of re-engagement advertisement, a custom URL scheme must be set for Acitvity defined in AndroidManifest.xml. Re-engagement measurement is performed by launching Activity by custom URL scheme.

<div id="tracking_ltv"></div>

## 4. Implement LTV tracking

By implementing LTV measurements at arbitrary conversion points such as membership registration, completion of tutorial, billing and etc, It measures Life Time Value for each advertisements of influx sources. In the case that LTV measurement is not necessary, it is able to omit this implementation.

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(LTV POINT ID);
```

In order to perform measure the LTV measurement, you need to specify the outcome point ID that identifies each achievement point. Please specify the issued ID in the argument of the `sendLtvConversion`.

In the case of performing the purchase measurement, please specify the billing amount and the currency code in the following manner at the point where the charging is complete.

```java
import jp.appAdForce.android.LtvManager;
// ...
LtvManager ltv = new LtvManager(ad);
ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
ltv.sendLtvConversion(LTV POINT ID);
```

Please specify the currency code defined in [ISO 4217](http://en.wikipedia.org/wiki/ISO_4217) for LtvManager.URL_PARAM_CURRENCY

[LTV tracking by using tag](./doc/ltv_browser/README.md)

<div id="tracking_analytics"></div>

## 5. Implement access analytics

You can measure comparison of Installation number of organic and non-organic, the number of the of activating application and unique users (DAU / MAU), user retention and so on. When the access analytics is not required, you can omit the implementation of this item.

To measure application launch and return from background, add `AnalyticsManager.sendStartSession` to onResume() of all activities.

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {
	@Override
	protected void onResume() {
		super.onResume();
		AdManager ad = new AdManager(this);
		ad.sendReengagementConversion(getIntent());

		AnalyticsManager.sendStartSession(this);
	}
}
```

> In case of creating several activities, add action on each onResume() As application comes from background, if nothing builds in activities, it cannot accurately track amount of active users.

[Event tracking by using access analytics](./doc/analytics_event/README.md)

[Purchase-event tracking by using access analytics](./doc/analytics_purchase/README.md)

<div id="use_proguard"></div>

## 6. In case of using ProGuard

In case of using proguard, set up as below to avoid warning notice.

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

Please check whether “keep” setting like below link, if you use Google Play Services SDK

[ProGuard configuration for using Google Play Services](https://developers.google.com/android/guides/setup#Proguard)

<div id="integration_test"></div>

## 7. Integration test

Until the application to market, test enough in a state where the the SDK has been introduced, and make sure that there is no problem in the operation of the application.

Communication of Installation measurement is performed only once after startup. If you want to do effect measurement test next, uninstall the application, please go from the installation again.

* **Test procedure**

1. Uninstall test app if it has been installed in the testing terminal.
1. Delete the Cookie of the default browser of the testing terminal.
1. Click the test URL that issued from our company
1. Redirected to the Market
1. Install a test application to the testing terminal
1. Activate the app, the browser will start-up
If the browser does not start, that means setting has not been carried out correctly. Review the settings, and if you do not see any problem, please contact us.
1. Screen transition to LTV point
1. Quit the app, also removed from the background
1. Activate the app again

Please tell the time of 3,6,7,9 to us. We will see if measurement has been successfully performed. If there are no problems in our confirmation, the test will be completed.

> Make sure that test URL will be requested on default browser of the terminal. The measurement will not be performed if you transit by Web View in the app such as mail app or QR code reader.

> When you click on a test URL, there is a case where error dialog is displayed because there is no transit destination, but this is not a problem in the communication test.

[Integration test for measurement reengagement](./doc/reengagement_test/README.md)

<div id="other_function"></div>

## 8. Implementation of other function

* [Implement opt out](./doc/optout/README.md)
* [Improvement of duplicated installation determination accuracy using auto backup in Android M(6.0)](./doc/auto_backup/README.md)

<div id="trouble_shooting"></div>

## 9. Trouble shooting

### 9.1. Application is released without setting of URL scheme and it does not transit to application from browser.

After starting external browsers to conduct Cookie measurement, it is necessary to go back to original screen by using URL scheme and transits to application. At this moment, it is necessary to set original URL scheme. If releasing without setting of URL scheme, it does not conduct such a transition.

### 9.2. URL scheme includes capital letters and small letters and it does not normally conduct transition to application.

Depending on the environment, the capital letters and small letters of URL scheme are not distinctive and it does not conduct transition of URL scheme as usual. Please set the URL scheme in all small letters and alphanumeric.

### 9.3. The value of installation confirmed in F.O.X is bigger than the number of Google Play Developer Console.

In F.O.X, by combining some formulations, conduct duplication install detection for terminal. WIthout the setting not conducting the detection, F.O.X judges that it is a new installation at every time of reinstallation even in same terminal.

In order to increase the accuracy of duplication install detection, make the following settings.

* [Setting of Google Play Services SDK for using Advertising ID](./doc/google_play_services/README.md)
* [Setting of deduplication using external storage](./doc/external_storage/README.md)
* [Improvement of duplicated installation determination accuracy using auto backup in Android M(6.0)](./doc/auto_backup/README.md)
