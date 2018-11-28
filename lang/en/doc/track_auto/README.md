[TOP](../../README.md)　>　Auto-Tracking

---

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/releases/tag/4.3.0)

# Auto-Tracking

* **[1. About Auto-Tracking](#about_autotracking)**
	* [1.1 Migrating from a previous version](#migration)
* **[2. SDK Activation and Enabling Auto-Tracking](#autotracking_enable)**
	* [2.1 FoxConfig Instancing](#foxconfig_instance)
	* [2.2 FoxConfig Annotation](#foxconfig_annotation)
* **[3. Auto-Tracking Options](#autotracking_option)**
	* [3.1 Selecting Options using FoxTrackOption](#autotracking_with_option)
	* [3.2 Implementing Custom Measurement Criteria](#autotracking_with_manual)
		* [3.2.1 Defining FoXConfig through Annotation](#define_foxconfig_annotation)
			* [Disabling Auto-Tracking on first launch](#off_annotation_auto_install_tracking)
			* [Disabling Auto-Tracking for Session measurement](#off_annotation_auto_session_tracking)
			* [Disabling Auto-Tracking for Deeplink tracking](#off_annotation_auto_deeplink_tracking)
		* [3.2.2 Defining a FoxConfig Instance](#define_foxconfig_instance)
			* [Disabling Auto-Tracking on first launch](#off_instance_auto_install_tracking)
			* [Disabling Auto-Tracking for Session measurement](#off_instance_auto_session_tracking)
			* [Disabling Auto-Tracking for Deeplink tracking](#off_instance_auto_deeplink_tracking)


<div id="about_autotracking"></div>

## 1. About Auto-Tracking

* This feature is supported by **F.O.X Android SDK 4.3.0** and above
* The following three measurement types are available.
  * First-time launch tracking
  * Session Tracking
  * Re-engagement measurement
* All other types of measurement function as previously.
* Some of these measurement types can still be performed manually.
* These can be used jointly with deferred deep linking.

<div id="migration"></div>

### 1.1 Migrating from a previous version

If your app was using any version of F.O.X past 4.X.X and you want to swap to Auto-Tracking then please refer to the material below. If you are implementing F.O.X for the first time, please skip this section. <br>
If you have been using any 3.X.x version of F.O.X then please go to our section on [Migrating to the latest version of F.O.X SDK](../migration/README.md)<br>
Below, we will explain the three measurement types that can be tracked automatically.

<div id="migration_install"></div>

#### [&nbsp;First-time App launch tracking&nbsp;]

The first measurement will be performed automatically when the application starts for the first time.
Please delete any `Fox.trackInstall()` methods which were contained in the `onCreate` method of the Activity which is displayed first when the app is launched.

[&nbsp;DELETE&nbsp;]

```java
Fox.trackInstall();
```

<div id="migration_session"></div>

#### [&nbsp;Session Tracking&nbsp;]

It is tracked automatically at application startup / return from background.
Please delete any `Fox.trackInstall()` methods which were contained in the `onResume` method of the Activity which is displayed first when the app is launched.

[&nbsp;DELETE&nbsp;]

```java
Fox.trackSession();
```


<div id="migration_deeplink"></div>

#### [&nbsp;deep link tracking&nbsp;]

Re-engagement measurement is performed through the `AndroidManifest.xml` as usual.
Please delete any `Fox.trackDeeplinkLaunch()` methods which were contained in the `onResume` method of the Activity which is displayed first when the app is launched.

[&nbsp;DELETE&nbsp;]

```java
Fox.trackDeeplinkLaunch(this);
```

<div id="autotracking_enable"></div>

## 2. SDK activation and Enabling auto-tracking execution

Activation can use any one of the following `FoxConfig`.
<div id="foxconfig_instance"></div>

### **2.1 FoxConfig Instancing**
&nbsp;&nbsp;&nbsp;Use [co.cyberz.fox.FoxConfig](../sdk_api/README.md#foxconfig)<br>
&nbsp;&nbsp;&nbsp; This is the configuration class that we use.<br>
&nbsp;&nbsp;&nbsp;For more details on its use before version 4.2.1, see [Previous Activation Methods](../../README.md#old_activation) or [FoxConfig](../sdk_api/README.md#foxconfig).

[&nbsp;**An Example of Implementing a FoxConfig Instance**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxConfig;

public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, issued AppID, "issued APP_KEY", "issued APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK Activation, start Auto-Tracking
      Fox.AUTOMATOR.init(config).startTrack();
  }

}
```

<div id="foxconfig_annotation"></div>

### **2.2 FoxConfig Annotation**
&nbsp;&nbsp;&nbsp;Use [co.cyberz.fox.annotation.FoxConfig](../sdk_api/README.md#annotation_foxconfig)<br>
&nbsp;&nbsp;&nbsp;This is the configuration class added for versions 4.3.0 and above.<br>
&nbsp;&nbsp;&nbsp;See [FoxConfig API](../sdk_api/README.md#annotation_foxconfig) for more info.

> * You cannot use both `co.cyberz.fox.FoxConfig` and `co.cyberz.fox.annotation.FoxConfig`at the same time .

[&nbsp;**An Example of Implementing a FoxConfig Annotation**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = issued AppID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();
      // SDK Activation,SDK Activation, start Auto-Tracking
      Fox.AUTOMATOR.init(this).startTrack();
  }

}
```

> * Be sure to define the FoxConfig annotation in the `Application` inheritance class.
>
> * Please implement Fox.AUTOMATOR in the `Application` inheritance class.

<div id="autotracking_option"></div>

## 3. Auto-Tracking Option

<div id="autotracking_with_option"></div>

### 3.1 Setting Options with FoxTrackOption

Detailed Auto-Tracking options can be found at[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption).

```java
@FoxConfig(appId = issued AppID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxTrackOption option = new FoxTrackOption();
      option.setRedirectUrl("myapp://top")
            .setBuid(getUserId())
            .setTrackingStateListener(new FoxTrackOption.TrackingStateListener() {
               @Override
               public void onComplete() {
                 // Run any code after tracking has completed

               }
      });
      Fox.AUTOMATOR.init(this).startTrack(option);
  }

}
```
> * It is possible to run `Fox.trackInstall ()` separately if you need to specify the Buid upon first app launch, and you can not prepare the Buid in the Application. The implementation is the same as it has been in the past.
>
> * It is possible to run `Fox.trackDeeplinkLaunch` separately if you need to specify the Buid upon Re-engagement measurement, and you can not prepare the Buid in the Application. The implementation is the same as it has been in the past.

<div id="autotracking_with_manual"></div>

### 3.2 Implementing Custom Measurement Criteria

It is possible to implement your own custom measurements.<br>
Please choose how from the two methods below.

<div id="define_foxconfig_annotation"></div>

#### 3.2.1 Defining FoXConfig through Annotation

You may use co.cyberz.fox.annotation.FoxConfig to change settings. All of them are set to `true` by default.
* `isAutoInstallTracking` : First time auto-install tracking boolean
* `isAutoSessionTracking` : Session auto-measurement boolean
* `isAutoDeeplinkTracking` : Deeplink auto-measurement boolean

<div id="off_annotation_auto_install_tracking"></div>

**Disabling Auto-Tracking on first launch**

Set `isAutoInstallTracking` to false, and implement it manually.

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = issued App ID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isAutoInstallTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      // Install tracking
      Fox.trackInstall();
  }
...
```
> Continue to insert [`Fox.trackInstall()`](../track_install/README.md) into the `onCreate` method in your Activity that launches your app.

<div id="off_annotation_auto_session_tracking"></div>

**Disabling Auto-Tracking for Session measurement**

Set `isAutoSessionTracking` to false, and implement it manually.

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = issued App ID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isAutoSessionTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // Session measurement
      Fox.trackSession();
  }
...
```
> Continue to insert `Fox.trackSession()` into the `onResume` method in each Activity that launches your app.

<div id="off_annotation_auto_deeplink_tracking"></div>

**Disabling Auto-Tracking for Deeplink tracking**

Set `isAutoDeeplinkTracking` to false, and implement it manually.

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = issued AppID, appKey = "issued APP_KEY", appSalt = "issued APP_SALT", isAutoDeeplinkTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // Re-engagement measurement
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> Continue to insert `Fox.trackDeeplinkLaunch()` into the `onResume` method in your Main Activity.

<div id="define_foxconfig_instance"></div>

#### 3.2.2 Defining a FoxConfig Instance

You may use co.cyberz.fox.Fox.AUTOMATOR to change settings. All of them are set to `false` by default.

* `setManualInstallTracking` : First time auto-install tracking settings
* `setManualSessionTracking` : Session measurement settings
* `setManualDeeplinkTracking` : Deeplink auto-measurement settings

<div id="off_instance_auto_install_tracking"></div>

**Disabling Auto-Tracking on first launch**

Set `setManualInstallTracking` to true, and implement it manually.

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, issued AppID, "issued APP_KEY", "issued APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK Activation, start Auto-Tracking
      Fox.AUTOMATOR.init(config)
                   .setManualInstallTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      // Run Install Tracking
      Fox.trackInstall();
  }
...
```
> Continue to insert [`Fox.trackInstall()`](../track_install/README.md) in the `onCreate` method in your Activity that first launches your app.

<div id="off_instance_auto_session_tracking"></div>

**Disabling Auto-Tracking for Session measurement**

Set `setManualSessionTracking` to true, and implement it manually.

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, issued AppID, "issued APP_KEY", "issued APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK Activation, start Auto-Tracking
      Fox.AUTOMATOR.init(config)
                   .setManualSessionTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // Session measurement
      Fox.trackSession();
  }
...
```
> Continue to insert `Fox.trackSession()` into the `onResume` method in each Activity that launches your app.

<div id="off_instance_auto_deeplink_tracking"></div>

**Disabling Auto-Tracking for Deeplink tracking**

Set `setManualDeeplinkTracking` to true, and implement it manually.

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, issued AppID, "issued APP_KEY", "issued APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK Activation, start Auto-Tracking
      Fox.AUTOMATOR.init(config)
                   .setManualDeeplinkTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // Re-engagement measurement
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> Continue to insert `Fox.trackDeeplinkLaunch()` into the `onResume` method in your Main Activity.


---
[Return to Top](../../README.md)
