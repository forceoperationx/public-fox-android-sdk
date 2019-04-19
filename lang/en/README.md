[![Language](http://img.shields.io/badge/language-java-red.svg?style=flat)](https://java.com)
[![Platform](http://img.shields.io/badge/platform-Android-green.svg?style=flat)](https://developer.android.com)
[![Android](http://img.shields.io/badge/support-API_Level_14+-green.svg?style=flat)](https://developer.android.com)

# About F.O.X

Force Operation X (Hereinafter referred to as F.O.X) is a total solution platform which measures and optimizes ad effectiveness on smartphone applications. Primary features such as user install tracking, LTV measurement, and CV measurement, will maximize the price-performance ratio of your app's advertising.<br>
Find out more about F.O.X at [cyber-z.co.jp](https://cyber-z.co.jp/en/fox).

# Summary

This is the Android SDK for F.O.X.

Read this in another language: [日本語](https://github.com/forceoperationx/public-fox-android-sdk/blob/4.x/lang/ja/README.md),[中文](https://github.com/forceoperationx/public-fox-android-sdk/blob/4.x/lang/zh-cn/README.md).


## Table of Contents

* **[About F.O.X SDK](#whats_fox_sdk)**
* **[1. Setting up the SDK](#install_sdk)**
	* [Adding the SDK to your project](https://github.com/forceoperationx/public_fox_android_sdk/releases)
	* [AndroidStudio projects](./doc/integration/android_studio/README.md)
	* [Eclipse projects](./doc/integration/eclipse/README.md)
	* [Migrating from a previous version](./doc/migration/README.md)
* **[2. Initial Settings](#setting_sdk)**
	* [2.1 Permission Settings](#setting_permission)
	* [2.2 Proguard Settings](#setting_proguard)
	* [2.3 Install Referrer Settings](#setting_installreferrer)
	* [2.4 Custom URL Scheme Settings](#setting_urlscheme)
	* [2.5 Setting Up Google Play Services](#setting_googleplayservices)
* **[3. F.O.X SDK Activation](#activate_sdk_into_app)**
	* [Auto-Tracking](./doc/track_auto/README.md)
* **[4. Implementing the install tracking feature](#tracking_install)**
	* [Install tracking details](./doc/track_install/README.md)
	* [Implementing deferred-deeplink](./doc/deferred_deeplink/README.md)
* **[5. Re-engagement measurement implementation](#tracking_reengagement)**
* **[6. LTV measurement](#tracking_event)**
	* [6.1 Session measurement](#tracking_session)
	* [6.2 Additional event measurement](#tracking_other_event)
	* [Event measurement details (engagement measurement, dynamic transmission linkage..)](./doc/track_events/README.md)
* **[7. Quick Integration Example](#quickly_integration)**
* **[8. Additional Feature Implementation](#other_function)**
	* [Enabling opt-out feature](./doc/optout/README.md)
	* [De-duplication feature settings (external storage permissions)](./doc/external_storage/README.md)
	* [De-duplication feature settings (auto-backup for Android M)](./doc/auto_backup/README.md)
* **[9. Frequent Troubleshooting Issues](#trouble_shooting)**
	* [FAQ・Notes](./doc/trouble_shooting/README.md)
* **[10. SDK API](#sdk_api)**


<div id="whats_fox_sdk"></div>

## About F.O.X SDK

By installing the F.O.X SDK, users can access the following features.

* **Install tracking**

Track and measure the number of installs along different advertising channels.

* **LTV measurement**

Measure Lifetime Value(LTV) along different advertising channels. Primary conversion points are member registrations, tutorial completions, and point of sales(billing). F.O.X monitors and maintains member registration rates, billing rates, and billing amounts per advertisement.

* **Site optimization analysis|Demographic analysis**

Compare organic and non-organic installs. Track app launches and unique users(DAU/MAU). Measure user retention and so on.

<div id="install_sdk"></div>

## 1. Installation

F.O.X Android SDK 4.0.0〜 supports versions `Android 4.0(API Level 14)` and above.

When integrating the F.O.X SDK into a gradle project, add the following to your project's build.gradle file.

```
repositories {
    maven {
        url "https://github.com/forceoperationx/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    implementation 'co.cyberz.fox:track-core:4.x.x'
}
```
For the exact sdk version please see our sdk [release page](https://github.com/forceoperationx/public_fox_android_sdk/releases). <br>
※ For manual installs, please download the most recent SDK.

Unzip the SDK and add `libs/FOX_Android_SDK_{VERSION}.jar` to the project.

**[additional details]**
* [How to integrate for an AndroidStudio project](./doc/integration/android_studio/README.md)
* [How to integrate for an Eclipse project](./doc/integration/eclipse/README.md)

> ※ If F.O.X is already integrated into your application, see [Migrating to the latest version of F.O.X SDK](./doc/migration/README.md) for more information.


<div id="setting_sdk"></div>

## 2. Settings

* **SDK Settings**

Some settings must be added to the AndroidManifest.xml file in order for the SDK to operate properly.

<div id="setting_permission"></div>

### 2.1 Version Settings

The F.O.X SDK utilizes the following two permissions.
Add them inside the &lt;Manifest&gt; tags.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Permission|Protection Level|Necessity|Summary
:---|:---:|:---:|:---
INTERNET|Normal|Necessary|Used to maintain the SDK's connection.
WRITE_EXTERNAL_STORAGE ※1|Dangerous|Optional|Used to improve de-duplication features.(※2)

> ※1 READ_EXTERNAL_STORAGE and WRITE_EXTERNAL_STORAGE permissions are used to read and write data to external storage. User actions such as app re-installs are more easily tracked by having these permissions enabled. However, these permissions are not necessary for the SDK to function.

> ※2 Beginning with Android Marshmallow(6.0), in order to use permissions with the ProtectionLevel of `dangerous`, users must accept those permissions at a prompt. See [De-duplication feature settings (external storage permissions)](./doc/external_storage/README.md) for more details.

<div id="setting_proguard"></div>

### 2.2 Proguard Settings
If an application is using Proguard, please add the following to ensure the F.O.X SDK does not
receive warnings.

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
### If you installed the SDK through Gradle, you may omit the following .jar file. ###
-libraryjars libs/FOX_Android_SDK_{VERSION}.jar
```
Additionally, if you have implemented the Google Play Services SDK, please follow the keep definitions listed in the link below.


[Proguard Settings with Google Play Services](https://developer.android.com/google/play-services/setup.html#Proguard)

<div id="setting_installreferrer"></div>

### 2.3 Install Referrer Settings
In order to accurately attribute an install of your app to its source, F.O.X needs information about the <strong>install referrer</strong>. This can be obtained through the <strong>Google Play Referrer API</strong> or by catching the <strong>Google Play Store intent</strong> with a broadcast receiver.



#### Using the Google Play Referrer API

This feature is supported for [![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.4.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.4.1)

You can attribute app installs by adding the [Google Play Referrer API](https://developer.android.com/google/play/installreferrer/library.html) dependency to your build.gradle file, as shown below. <br>
(There is no need to run the SDK)

```
dependencies {
    implementation 'com.android.installreferrer:installreferrer:1.0'
}
```

#### Using multiple INSTALL REFERRER receiver

If your application already defines a receiver class which corresponds to "com.android.vending.INSTALL_REFERRER", see
[Enabling multiple INSTALL_REFERRER receivers](./doc/install_referrer/README.md).

<div id="setting_urlscheme"></div>

#### F.O.X SDK < 4.4.0

If you want to use a broadcast receiver, add the following `receiver` tag inside the `application` tag in your `AndroidManifest.xml`.

```xml
<receiver android:name="co.cyberz.fox.FoxInstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```


### 2.4 Custom URL Scheme Settings

In order to grant an external browser the capability to launch your app, add the following inside the `activity` tag responsible for launching your application. <br>


```xml
<activity
    android:name=".FirstActivity"
    android:screenOrientation="portrait"
    android:launchMode="singleTop">
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>

    <!-- Custom URL Scheme settings for launching the application -->
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="yourcustomURLscheme" />
    </intent-filter>
</activity>
```

Please confirm the following.

* When performing cookie tracking, set the scheme to the activity in which the browser resumes your application
* Set the scheme to the activity performing [re-engagement measuring](#tracking_reengagement)

> ※ When using cookie tracking, we recommend setting the customURLScheme to the [cookie tracking](#tracking_install) activity in which the browser resumes your application.

> ※ We recommend setting the `launch mode` of your re-engagement measuring/cookie tracking activity to `singleTop`.

> ※ Depending on your production environment, there are cases where capitalized URL schemes are not recognized. In order to avoid this, please set your URL scheme in lower case alphanumeric characters.

> ※ For more details on Custom URL settings, please refer to the [Android Developers (see implicit intents) ](https://developer.android.com/guide/components/intents-filters.html#Receiving) page.

<div id="setting_googleplayservices"></div>

### 2.5 Setting Up Google Play Services

As of August 1st, 2014, apps in the Google PLay Store are required to use the [Google Advertising ID](https://support.google.com/googleplay/android-developer/answer/6048248?hl=en) to uniquely identify devices. In order for F.O.X to use the Google Advertising ID, you must integrate [Google Play Services](https://developers.google.com/android/guides/setup) into your app.
If it is set up through gradle, add the following `dependencies` to your `build.gradle` file.
```
	implementation 'com.google.android.gms:play-services:9.4.0'
```

If you are only setting up GooglePlayServices to obtain the Google Advertising ID, only add the following.

```
	implementation 'com.google.android.gms:play-services-ads:9.4.0'
```

> The F.O.X SDK will still run, even if the Google Play Services SDK is not set up. However, because the Google Advertising ID cannot be obtained, some advertisement menus on the tool cannot be used, and the deduplication function's accuracy will decline. <br> For more detailed instructions, see [Google Play Services and the Google Advertising ID](./doc/google_play_services/README.md).

### AndroidManifest.xml Sample

[AndroidManifest.xml Sample](./doc/config_android_manifest/AndroidManifest.xml)


<div id="activate_sdk_into_app"></div>

## 3. F.O.X SDK Activation

### 3.1 Activation

In order to activate the F.O.X SDK, you must follow these steps:

* Create or use a class that extends `Application`
* Find or create the `onCreate` method in your `Application` class and insert the `FoXConfig` class
<br> <br>
Additionally, it is necessary to set the `android:name` value in your `AndroidManifest.xml` to the name of your application prefixed by a dot as shown below.

**Sample Manifest Settings**

```xml
<application
   android:name=".YourApplication"
	 android:allowBackup="true"
	 android:icon="@mipmap/ic_launcher"
	 android:label="@string/app_name">
	 .
	 ..
	 ...
</application>
```

**Setting up the `Application` class**

<div id="new_activation"></div>

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;activation and auto-tracking execution&nbsp;]


```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId =  AppID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDK Activation, Auto-tracking execution
        Fox.AUTOMATOR.init(this).startTrack();
    }
}
```

> ※ After your application has been registered, you may find the values for appID, salt, and appKey at F.O.X Management App List > find your application from the dropdown list on the top right of the page > and check the SDK installation settings.

> The above written code both activates the SDK and performs the auto-tracking execution functions. If you are already implementing an earlier version, see [Auto-tracking details](./doc/track_auto/README.md) for how to update.

* [Auto-Tracking details](./doc/track_auto/README.md)

<div id="old_activation"></div>

&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;Activation Execution（Manual Settings）&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.FoxConfig;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        int FOX_APP_ID = issued appID;
        String FOX_APP_KEY = "issued APP_KEY";
        String FOX_APP_SALT = "issued APP_SALT";
        FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
				  config.addDebugOption(BuildConfig.DEBUG)
						.activate();
    }
}

```

<div id="tracking_install"></div>

### 3.2 Offline Mode
The following are instructions on how to stop the F.O.X SDK from performing measurement and tracking. <br>
If you want to enable offline mode, set addOfflineModeOption equal to `true` or set it to `false` to disable it. (By default, offline mode is disabled). <br>

- Use this feature if you want to prevent (location) data from being sent to F.O.X during production
- If you want to use this feature to enable/disable based on user permissions, after the user's action, run the activate() method. (activate() is run on launch and is required)
- auto-tracking will not occur if offline mode is enabled. In this case, perform manual tracking.
- Once offline mode is set, it will persist until the application is uninstalled.
```java
	FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
	// Offline Mode disabled
	if (!isUserPermissionSavedByApp) {
		config.addOfflineModeOption(isOfflineByApp); //Once offline mode is enabled, it will save this setting until the application is uninstalled
	}
	config.addDebugOption(BuildConfig.DEBUG).activate(); //The activate() method enables F.O.X. Make sure this method is run on application launch.
```

## 4. Implementing Install Tracking

You can perform advertising effectiveness measurement by tracking the first application launch event. <br>
Insert the following [`Fox.trackInstall`](./doc/auto_backup/README.md) method into the onCreate method of the Activity which launches your application. <br>

```java
import co.cyberz.fox.Fox;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);
	// Install tracking
	Fox.trackInstall();
}
```
> ※ Even if the trackInstall method is called multiple times it will not execute more than once.
>
> ※ You may define options on the trackInstall method. For further details see [Install Tracking details](./doc/track_install/README.md)
>
> ※ Starting from SDK Version 4.3.0 this implementation can be omitted. (not necessary when auto-tracking is enabled)

* [Install Tracking Details](./doc/track_install/README.md)

* [Deferred Deeplink Implementation](./doc/deferred_deeplink/README.md)

<div id="tracking_reengagement"></div>

## 5. Re-engagement Measurement Implementation

When performing re-engagement advertisement measurement (app launches by URL Scheme), run the [`Fox.trackDeeplinkLaunch`](./doc/sdk_api/README.md#foxconfig) method inside the onResume() method of `every Activity which contains a custom URL Scheme`.

```java
co.cyberz.fox.Fox;


@Override
protected void onNewIntent(Intent intent)
{
	super.onNewIntent(intent);
	setIntent(intent);
}

@Override
protected void onResume() {
	super.onResume();
	// Re-engagement Measurement
	Fox.trackDeeplinkLaunch(this);
}
```
> ※ In considering a situation where the Activity launched by a URL Scheme has its launchMode set to "singleTask" or "singleInstance", override the 'onNewIntent' method and call the 'setIntent' method to retrieve the most recent Intent.
>
> ※ Defining the [Custom URL Scheme](#setting_urlscheme) for the Activity listed in the AndroidManifest.xml file is a pre-condition for performing re-engagement advertisement measurement.
>
> ※ Starting from SDK Version 4.3.0 this implementation can be omitted. (not necessary when auto-tracking is enabled)

<div id="tracking_event"></div>

## 6. In-App event Measurement

* [Event Measurement Details](./doc/track_events/README.md)

<div id="tracking_session"></div>

### 6.1 Implementing Session Measurement

Compare organic and non-organic installs. Track app launches and unique users(DAU/MAU). Measure user retention and so on.

To measure application launches and returns from the background, add the [`Fox.trackSession`](./doc/sdk_api/README.md#fox) method to each onCreate method in each activity, or add it to your BaseActivity.

※ Make sure session measurement (trackSession) gets called after [Install Tracking](#tracking_install)

```java
import co.cyberz.fox.Fox;

public class BaseActivity extends Activity {

	@Override
	protected void onResume() {
		super.onResume();
		// Session Tracking
		Fox.trackSession();
	}
}
```

> ※ Starting from SDK Version 4.3.0 this implementation can be omitted. (not necessary when auto-tracking is enabled)

**[In case of Implementing Session-start Measurement in every Activity]**

By adding the following code to the above-mentioned class which inherits from the `Application` class introduced in [Activation](#activate_sdk_into_app) you can reduce the redundancy of having to add it to every Activity's onResume method. <br>
However, if you are using [`registerActivityLifecycleCallbacks`](https://developer.android.com/reference/android/app/Application.html#registerActivityLifecycleCallbacks(android.app.Application.ActivityLifecycleCallbacks) then your application's minSDKVersion must be 14 and above.

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // Activation source
        private int FOX_APP_ID = issued アプリID;
        private String FOX_APP_KEY = "issued APP_KEY";
        private String FOX_APP_SALT = "issued APP_SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // Application Lifecycle detection
        if (14 <= Build.VERSION.SDK_INT) {
        	registerActivityLifecycleCallbacks(new ApplicationLifeCycleCallbacks());
        }
    }

    private static final class ApplicationLifeCycleCallbacks implements ActivityLifecycleCallbacks {

	    @Override
	    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
	    }

    ...

	    @Override
	    public void onActivityResumed(Activity activity) {
	      // Session Tracking
	      Fox.trackSession();
	    }
    ...
  }
}

```

<div id="tracking_other_event"></div>

### 6.2 Additional event measurement

By measuring app events such as member registrations, tutorial completions, In-App Purchases and other such conversion points, you can track the LTV of individual advertisements. 


※ Make sure application event tracking  (trackSession) gets called after [Install Tracking](#tracking_install) and [Session-start Measurement](#tracking_session)

<div id="tracking_event_tutorial"></div>

**[Tutorial event measurement example]**

```java
import co.cyberz.fox.service.FoxEvent;
import co.cyberz.fox.Fox;

 ...

FoxEvent tutorialEvent = new FoxEvent("_tutorial_comp", conversionpointID);
tutorialEvent.buid = "ユーザーID";
Fox.trackEvent(tutorialEvent);
```
> In order to measure the LTV of each event, you need to specify the `conversion point ID` that identifies each conversion point. Please specify the event name and issued ID as an argument in the [`FoxEvent`](./doc/sdk_api/README.md#foxevent) class' constructor.

<div id="tracking_event_purchase"></div>

**[In-App Purchase example]**

When measuring purchase events, please specify the purchase amount and currency code at the point where the transaction completes as follows:

```java
import co.cyberz.fox.service.FoxEvent;
import co.cyberz.fox.Fox;
...
FoxEvent purchaseEvent = new FoxEvent("_purchase", 成果地点ID);
purchaseEvent.price = 9.99;
purchaseEvent.currency = "USD";
purchaseEvent.sku = "ITEM_1";
Fox.trackEvent(purchaseEvent);
```

Please specify the currency code as defined in [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)

[LTV Measurement Details](./doc/track_events/README.md)

<div id="quickly_integration"></div>

## 7. Quick integration

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;

These four functions can be replicated using the following source code:

* F.O.X SDK activation
* First-time App launch tracking
* Session event tracking
* Re-engagement measurement

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = issued App ID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT")
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDK Activation, Auto-tracking execution
        Fox.AUTOMATOR.init(this).startTrack();
    }
}

```

> ※ Please share the custom URL Scheme you set in your AndroidManifest and main Activity with the F.O.X team when performing cookie measurement. (when implementing a browser user agent)


<div id="other_function"></div>

## 8. Additional Feature Implementation

* [Enabling opt-out feature](./doc/optout/README.md)

* [De-duplication feature settings (external storage permissions)](./doc/external_storage/README.md)

* [De-duplication feature settings (auto-backup for Android M)](./doc/auto_backup/README.md)

<div id="trouble_shooting"></div>

## 9. Frequent Troubleshooting Issues

* [FAQ・Notes](./doc/trouble_shooting/README.md)

<div id="sdk_api"></div>

## 10. SDK API

* [SDK API](./doc/sdk_api/README.md)
