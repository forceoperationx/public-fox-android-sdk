[TOP](../../README.md)　>　自动计测的详细

---

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.3.0)

# 自动计测的详细

* **[1. 关于自动计测](#about_autotracking)**
	* [1.1 从旧版本的升级](#migration)
* **[2. SDK激活和自动计测有效化](#autotracking_enable)**
	* [2.1 FoxConfig实例](#foxconfig_instance)
	* [2.2 FoxConfig注解](#foxconfig_annotation)
* **[3. 自动计测选项](#autotracking_option)**
	* [3.1 使用FoxTrackOption来设置选项](#autotracking_with_option)
	* [3.2 个别手动执行特定的计测种类](#autotracking_with_manual)
		* [3.2.1 使用注解定义FoxConfig](#define_foxconfig_annotation)
			* [初次启动计测的自动计测设置为OFF](#off_annotation_auto_install_tracking)
			* [Session计测的自动计测设置为OFF](#off_annotation_auto_session_tracking)
			* [Deeplink经由计测的自动计测设置为OFF](#off_annotation_auto_deeplink_tracking)
		* [3.2.2 使用实例定义FoxConfig](#define_foxconfig_instance)
			* [初次启动计测的自动计测设置为OFF](#off_instance_auto_install_tracking)
			* [Session计测的自动计测设置为OFF](#off_instance_auto_session_tracking)
			* [Deeplink经由计测的自动计测设置为OFF](#off_instance_auto_deeplink_tracking)


<div id="about_autotracking"></div>

## 1. 关于自动计测

* 本功能**从F.O.X Android SDK 4.3.0开始才可以利用**。
* 作为计测种类的对象有以下3种。
  * 初次启动计测
  * Session计测
  * 流失唤回计测
* 其他的付费等APP内事件的计测和过去的编码执行方式一样。
* 可以手动编码处理执行一部分的计测。
* 可以和Deferred Deeplinking并用。

<div id="migration"></div>

### 1.1 从旧版本的升级

使用F.O.X Android SDK 4.X.X变更为执行自动计测的话，请参考本章下面的内容。如果是初次导入SDK的话，可以不用查看这里的说明内容。<br>
到目前为止使用F.O.X Android SDK 3.X.X的客户，烦请首先查看[升级为最新版本](../migration/README.md)<br>
下面针对自动计测对象的3个计测种类分别做说明。

<div id="migration_install"></div>

#### [&nbsp;初回起動計測&nbsp;]

在APP启动的时刻，自动进行初次启动计测。<br>
到目前为止在APP启动时最初表示的Activity里有onCreate方法，请删除onCreate方法里执行的Fox.trackInstall()。

[&nbsp;删除&nbsp;]

```java
Fox.trackInstall();
```

> ※ 仅将初次启动计测从自动计测对象里去除的时候&nbsp;请参考[`3.2 个别手动执行特定的计测种类`](#autotracking_with_manual)。

<div id="migration_session"></div>

#### [&nbsp;Session计测&nbsp;]

和一直以来（在Activity跳转时计测）的执行有所不同，在APP的启动时或从后台恢复到前台时自动执行计测。<br>
请删除到目前为止在所有Activity的onResume方法中执行的Fox.trackSession()方法。

[&nbsp;删除&nbsp;]

```java
Fox.trackSession();
```

> ※ 仅将初次Session计测从自动计测对象里去除的时候&nbsp;请参考[`3.2 个别手动执行特定的计测种类`](#autotracking_with_manual)。

<div id="migration_deeplink"></div>

#### [&nbsp;Deeplink经由计测&nbsp;]

为了进行流失唤回（Reengagement）广告计测，按以往的方式是以在AndroidManifest.xml里定义的Activity里[`设置定制化的URL Scheme`](../../README.md#setting_urlscheme)为前提。<br>
到目前为止在APP启动时最初表示的Activity里有onResume()方法，请删除onResume()方法里执行的Fox.trackDeeplinkLaunch()方法。


[&nbsp;删除&nbsp;]

```java
Fox.trackDeeplinkLaunch(this);
```

> ※ 仅将Deeplink经由计测从自动计测对象里去除的时候&nbsp;请参考[`3.2 个别手动执行特定的计测种类`](#autotracking_with_manual)。

<div id="autotracking_enable"></div>

## 2. SDK激活和自动计测有效化

为了激活SDK请使用如下任意一个`FoxConfig`。

<div id="foxconfig_instance"></div>

### **2.1 FoxConfig实例**
&nbsp;&nbsp;&nbsp;使用[co.cyberz.fox.FoxConfig](../sdk_api/README.md#foxconfig)。<br>
&nbsp;&nbsp;&nbsp;这是到4.2.1为止使用的Configuration Class。在此省略说明使用方法。<br>
&nbsp;&nbsp;&nbsp;详情请查看[以往的激活方式](../../README.md#old_activation)或者[FoxConfig](../sdk_api/README.md#foxconfig)。

[&nbsp;**使用实例来定义FoxConfig的例子**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxConfig;

public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 发行的APPID, "发行的APP_KEY", "发行的APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK激活、自动计测开始
      Fox.AUTOMATOR.init(config).startTrack();
  }

}
```

<div id="foxconfig_annotation"></div>

### **2.2 FoxConfig注解**
&nbsp;&nbsp;&nbsp;使用[co.cyberz.fox.annotation.FoxConfig](../sdk_api/README.md#annotation_foxconfig)。<br>
&nbsp;&nbsp;&nbsp;使用从4.3.0开始添加的注解定义必须信息的Configuration Class。<br>
&nbsp;&nbsp;&nbsp;[FoxConfig API详细](../sdk_api/README.md#annotation_foxconfig)

> * `co.cyberz.fox.FoxConfig`和`co.cyberz.fox.annotation.FoxConfig`无法并用。

[&nbsp;**使用FoxConfig注解执行的例子**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();
      // SDK激活、自动计测开始
      Fox.AUTOMATOR.init(this).startTrack();
  }

}
```

> * 请务必将FoxConfig注解定义到Application继承类里。
>
> * 请将Fox.AUTOMATOR放在Application继承类里来执行。

<div id="autotracking_option"></div>

## 3. 自动计测选项

<div id="autotracking_with_option"></div>

### 3.1 使用FoxTrackOption来设置选项

有关自动计测的计测选项使用[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption)。

```java
@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isDebug = BuildConfig.DEBUG)
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
                 // 计测完成时执行的处理

               }
      });
      Fox.AUTOMATOR.init(this).startTrack(option);
  }

}
```

> ※ 初次启动计测时需要设置Buid，但在Application里没有准备Buid的话、可以另外执行`Fox.trackInstall()`。执行位置和以往的地方一样，为主要的Activity的onCreate()方法。
>
> ※ 在流失唤回广告计测时需要设置Buid，但在Application里没有准备Buid的话、可以另外执行`Fox.trackDeeplinkLaunch`。

<div id="autotracking_with_manual"></div>

### 3.2 个别手动执行特定的计测种类

从自动计测对象里去除掉，可以手动安装进行个别执行计测。<br>
根据开发方法请选择下面两个模式。

<div id="define_foxconfig_annotation"></div>

#### 3.2.1 使用注解定义FoxConfig

使用co.cyberz.fox.annotation.FoxConfig的下面3各字段来做设置。任何一个默认都为true。
* `isAutoInstallTracking` : 针对初次启动计测设置是否执行自动计测
* `isAutoSessionTracking` : 针对Session计测设置是否执行自动计测
* `isAutoDeeplinkTracking` : 针对DeepLink系的计测设置是否执行自动计测

<div id="off_annotation_auto_install_tracking"></div>

**初次启动计测的自动计测设置为OFF**

把`isAutoInstallTracking`设置为false、做个别执行。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isAutoInstallTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      // Install计测执行
      Fox.trackInstall();
  }
...
```
> 按以往的那样，在APP启动的Activity的onCreate方法里执行[`Fox.trackInstall()`](../track_install/README.md)。

<div id="off_annotation_auto_session_tracking"></div>

**Session计测的自动计测设置为OFF**

把`isAutoSessionTracking`设置为false、做个别执行。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isAutoSessionTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // Session计测
      Fox.trackSession();
  }
...
```
> 按以往的那样，在各Activity的onResume方法里执行`Fox.trackSession()`。

<div id="off_annotation_auto_deeplink_tracking"></div>

**Deeplink经由计测的自动计测设置为OFF**

把`isAutoDeeplinkTracking`设置为false、做个别执行。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isAutoDeeplinkTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // 流失唤回广告计测
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> 按以往的那样，在主要的Activity的onResume方法里执行`Fox.trackDeeplinkLaunch()`。

<div id="define_foxconfig_instance"></div>

#### 3.2.2 使用实例定义FoxConfig

在co.cyberz.fox.Fox.AUTOMATOR里做如下三个设置。每一个默认都为false。

* `setManualInstallTracking` : 针对初次启动计测设置是否执行自动计测
* `setManualSessionTracking` : 针对Session计测设置是否执行自动计测
* `setManualDeeplinkTracking` : 针对DeepLink系的计测设置是否执行自动计测

<div id="off_instance_auto_install_tracking"></div>

**初次启动计测的自动计测设置为OFF**

把`setManualInstallTracking`设置为true、做个别执行。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 发行的APPID, "发行的APP_KEY", "发行的APP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK激活、自动计测开始(仅将初次启动计测设置为手动执行)
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
      // 执行Install计测
      Fox.trackInstall();
  }
...
```
> 按以往的那样，在APP启动的Activity的onCreate方法里执行[`Fox.trackInstall()`](../track_install/README.md)。

<div id="off_instance_auto_session_tracking"></div>

**Session计测的自动计测设置为OFF**

`setManualSessionTracking`にtrueを指定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK激活、自动计测开始(仅将Session计测设置为手动执行)
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
      // Session计测
      Fox.trackSession();
  }
...
```
> 按以往的那样，在各Activity的onResume方法里执行`Fox.trackSession()`。

<div id="off_instance_auto_deeplink_tracking"></div>

**Deeplink经由计测的自动计测设置为OFF**

把`setManualDeeplinkTracking`设置为true、做个别执行。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDK激活、自动计测开始(仅将Deeplink经由计测设置为手动执行)
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
      // 流失唤回广告计测
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> 按以往的那样，在主要的Activity的onResume方法里执行`Fox.trackDeeplinkLaunch()`。


---
[Top](../../README.md)
