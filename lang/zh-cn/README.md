[![Language](http://img.shields.io/badge/language-java-red.svg?style=flat)](https://java.com)
[![Platform](http://img.shields.io/badge/platform-Android-green.svg?style=flat)](https://developer.android.com)
[![Android](http://img.shields.io/badge/support-API_Level_14+-green.svg?style=flat)](https://developer.android.com)

# 什么是Force Operation X

Force Operation X（以下简称F.O.X）是一款用于优化智能手机广告效果的整体解决方案平台。不光是App的下载和网页用户行为的计测，基于智能用户行为特征的独特计测基准，能够实现企业推广的性价比最大化。

本文件是为实现智能手机APP中广告效果最大化的F.O.X SDK导入与安装的说明书。

## 目录

* **[什么是F.O.X SDK](#whats_fox_sdk)**
* **[1.导入](#install_sdk)**
	* [下载SDK](https://github.com/forceoperationx/public-fox-android-sdk/releases)
	* [AndroidStudio项目的导入方法](./doc/integration/android_studio/README.md)
	* [Eclipse项目的导入方法](./doc/integration/eclipse/README.md)
	* [从旧版本转移](./doc/migration/README.md)
* **[2. 设置](#setting_sdk)**
	* [2.1 设置权限](#setting_permission)
	* [2.2 设置Proguard](#setting_proguard)
	* [2.3 设置Install Referrer计测](#setting_installreferrer)
	* [2.4 设置自定义URL SCHEME](#setting_urlscheme)
	* [2.5 为使用广告ID的Google Play Services导入](#setting_googleplayservices)
* **[3. 激活F.O.X SDK](#activate_sdk_into_app)**
	* [自动计测的详细](./doc/track_auto/README.md)
* **[4. Install计测的安装](#tracking_install)**
	* [Install计测的详细](./doc/track_install/README.md)
	* [Deferred Deeplink的执行](./doc/deferred_deeplink/README.md)
* **[5. 流失唤回广告（Reengagement）计测的安装](#tracking_reengagement)**
* **[6. APP内事件的计测](#tracking_event)**
	* [6.1 Session（启动事件）的计测](#tracking_session)
	* [6.2 其他APP内事件的计测](#tracking_other_event)
	* [事件计测的详细 (Engagement计测・动态投放连动..)](./doc/track_events/README.md)
* **[7. 最快实际安装案例](#quickly_integration)**
* **[8. 安装其他功能](#other_function)**
	* [安装Optout](./doc/optout/README.md)
	* [利用外部储存排除重复的设定](./doc/external_storage/README.md)
	* [自动备份功能的使用 (Android M)](./doc/auto_backup/README.md)
* **[9. 最后的注意事项](#trouble_shooting)**
	* [FAQ・注意事项](./doc/trouble_shooting/README.md)
* **[10. SDK API](#sdk_api)**


<div id="whats_fox_sdk"></div>

## 什么是F.O.X SDK

将F.O.X SDK导入APP之后，能够实现以下功能。

* **Install计测**

能够计测不同广告带来的安装数。

* **LTV计测**

可以计测不同广告来源的Life Time Value。主要的成果地点为会员注册、新手引导完成、付费等。能够分别监测各广告的登录率、付费率以及付费金额。

* **流量分析**

比较自然流入和广告流入带来的安装。能够计测App的启动次数和唯一用户数(DAU/MAU)、留存率等。

<div id="install_sdk"></div>

## 1. 导入

F.O.X Android SDK 4.0.0〜 支持`Android 4.0(API Level 14)` 及以上版本。

使用Gradle来导入F.O.X SDK module时，请将以下设置写入项目build.gradle。

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


dependencies中指定的SDK版本号与下面下载页面中一致，请在下载页面去确认后下载<br>
如果是使用本地jar来手动安装的话，请从release页面下载最新版本的zip文件。

* [SDK下载](https://github.com/forceoperationx/public-fox-android-sdk/releases)

打开下载的SDK文件、在APP项目中导入`libs/FOX_Android_SDK_{VERSION}.jar`。

**[详细信息]**
* [AndroidStudio项目的导入方法](./doc/integration/android_studio/README.md)
* [Eclipse项目的导入方法](./doc/integration/eclipse/README.md)

> ※ 如果APP中已经安装了SDK，请参考[升级更新到最新版本](./doc/migration/README.md)

<div id="setting_sdk"></div>

## 2. 设置

* **SDK设置**

在AndroidManifest.xml中添加SDK运行所需的设置。

<div id="setting_permission"></div>

### 2.1 设置权限

F.O.X SDK可以使用以下三种权限。 &lt;Manifest&gt;Tag中添加以下权限的设置。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

权限|Protection Level|必須|概要
:---|:---:|:---:|:---
INTERNET|Normal|必須|F.O.X SDK进行通信的必要条件。
WRITE_EXTERNAL_STORAGE ※1|Dangerous|任意|使用外部存储来优化排除重复功能时必须设置。(※2)

> ※1 READ_EXTERNAL_STORAGE和WRITE_EXTERNAL_STORAGE权限，在外部储存中记录数据可以使APP重新安装时更准确的计测，但不是必须项目。

> ※2 从Android M开始，使用ProtectionLevel被指定为dangerous并需要权限的功能时，需要用户许可。具体请参考[使用外部储存来优化排除重复功能](./doc/external_storage/README.md)。

<div id="setting_proguard"></div>

### 2.2 设置Proguard
使用Proguard进行APP代码混淆时，为排除F.O.X SDK的调用方法，请添加以下设置。

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
# 通过Gradle安装SDK时，不需要指定以下jar文件。
-libraryjars libs/FOX_Android_SDK_{VERSION}.jar
```

另外，在已安装Google Play Service SDK 的情况下，请确认以下页面中是否已记述keep指定。

[Google Play Services导入时的Proguard对应](https://developer.android.com/google/play-services/setup.html#Proguard)

<div id="setting_installreferrer"></div>

### 2.3 设置install referer计测
使用install referer进行install计测时，请把以下设置追加到&lt;application&gt;tag里。

```xml
<receiver android:name="co.cyberz.fox.FoxInstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

#### 使用Google Play Referrer API

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.4.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.4.1)

通过将下面的[Google Play Referrer API](https://developer.android.com/google/play/installreferrer/library.html)的dependency添加到build.gradle的方式，可以利用此API实现InstallReferrer计测。<br>
(不需要特意去安装SDK)

```
dependencies {
    implementation 'com.android.installreferrer:installreferrer:1.0'
}
```

"com.android.vending.INSTALL_REFERRER"的Receiver 类已经被定义的情况下，请参照[让多个INSTALL_REFERRER RECEIVER共存的设置](./doc/install_referrer/README.md)。

<div id="setting_urlscheme"></div>

### 2.4 设置自定义URL SCHEME

为了能够从外部启动APP，请在启动对象的&lt;activity&gt;tag中添加下列设置。<br>

```xml
<activity
    android:name=".FirstActivity"
    android:screenOrientation="portrait"
    android:launchMode="singleTop">
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>

    <!-- APP启动时所需的自定义URL SCHEME设置 -->
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="填写任意的URL Scheme" />
    </intent-filter>
</activity>
```

<br>以下为主要案例。

* 按照Cookie计测手法时，设置到浏览器返回APP时的Activity里设置流失唤回广告计测的Activity
* 设置到进行[流失唤回广告(Reengagement)计测](#tracking_reengagement)的Activity里

> ※使用Cookie计测手法从浏览器返回APP时，推荐自定义URL SCHEME指定到[安装实现Cookie计测](#tracking_install)的Activity上。

> ※ Cookie计测和流失唤回广告计测时，建议将Activity的`launchMode`设置为`singleTop`。

> ※ 根据运行环境，可能会有URL SCHEME的大小写文字不能识别而导致无法迁移的情况。请将URL SCHEME全部设置为半角小写英文数字或小数点。

> ※ 自定义URL SCHEME详细请查看[Android Developers（接收隐式Intent）](https://developer.android.com/guide/components/intents-filters.html#Receiving)。

<div id="setting_googleplayservices"></div>

### 2.5 导入Google Play Services来使用广告ID

为使用广告ID，需先导入Google Play Services。<br>
使用Gradle进行导入时，在要导入的APP的`build.gradle`的`dependencies`中加入以下设置。

```
	implementation 'com.google.android.gms:play-services:9.4.0'
```

如果仅是为获取广告ID而导入GooglePlayServices，使用下面的最小指定就可以。

```
	implementation 'com.google.android.gms:play-services-ads:9.4.0'
```

> 如果没有导入Google Play Services SDK却启动F.O.X SDK，会因为没有使用广告ID导致部分的广告菜单计测无法进行，或重复判定的准确率较低等问题。<br>
导入的具体步骤请参照[导入Google Play Services来使用广告ID](./doc/google_play_services/README.md)。


### AndroidManifest.xml实例

[AndroidManifest.xml实例](./doc/config_android_manifest/AndroidManifest.xml)


<div id="activate_sdk_into_app"></div>

## 3. 激活F.O.X SDK

### 3.1 激活

为激活F.O.X SDK，需在继承了Application类的onCreate方法中安装[`FoxConfig`](./doc/sdk_api/README.md#foxconfig)类。
并且，继承Application的类必须指定AndroidManifest.xml中Application Tag的name属性。

**AndroidManifest的Application继承类的设置实例**

```mxl
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

**Application继承类的安装**

<div id="new_activation"></div>

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;激活兼自动计测的执行&nbsp;]


```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDK激活和自动计测的开始
        Fox.AUTOMATOR.init(this).startTrack();
    }
}

```
> ※ APP登录以后，可以在F.O.X管理画面的【APP一览>此APP右上方的设定按钮>导入SDK】的地方来确认appId、salt、appKey的值。

> 上面的代码可以同时执行激活和自动计测。已经导入了旧版本的话，请务必去确认[自动计测的详细](./doc/track_auto/README.md)的内容。

* [自动计测的详细](./doc/track_auto/README.md)

<div id="old_activation"></div>

&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;进行激活（手动设定）&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.FoxConfig;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        int FOX_APP_ID = 发行的APPID;
        String FOX_APP_KEY = "发行的APP_KEY";
        String FOX_APP_SALT = "发行的APP_SALT";
        FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
				  config.addDebugOption(BuildConfig.DEBUG)
						.activate();
    }
}

```

<div id="tracking_install"></div>

### 3.2 线下模式
开启线下模式功能，可停止F.O.X SDK的所有监测行为。<br>
将addOfflineModeOption设定为true来开启线下模式，设定为false来关闭线下模式（未设定时默认为关闭线下模式）。

- 在开发期间，如不想将数据发送到F.O.X，或者希望按照投放地域停止计测的时候，可以利用这个功能。
- 根据用户许可来设定线下模式是否开启时，请确保在用户许可之后执行activate()。(activate()需要在App每次启动时去执行)
- 自动计测的代码实装方式不适用于线下模式。请按手动计测来实装代码。
- 设定将保持生效至App被删除。
```java
	FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
	// 如果未开启线下模式
	if (!isUserPermissionSavedByApp) {
		config.addOfflineModeOption(isOfflineByApp); //一旦开启，设定将保持生效至App被删除。
	}
	config.addDebugOption(BuildConfig.DEBUG).activate(); //activate()是让F.O.X生效的方法。请务必在APP启动的时候执行。
```

## 4. Install计测的安装
进行初次启动的Install计测，可以计测广告效果。<br>
在启动APP时调用的Activity的onCreate方法中安装下面的[`Fox.trackInstall`](./doc/auto_backup/README.md#fox)。<br>

```java
import co.cyberz.fox.Fox;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);
	// Insatll计测
	Fox.trackInstall();
}
```

> ※ 第二次启动及以后，即使调用trackInstall方法也不会被执行。
>
> ※ 在trackInstall方法中可以指定option参数，具体请查看[Install计测的详细](./doc/track_install/README.md)。
>
> ※ 从SDK4.3.0版本开始这部分的代码就可以省略了。（将自动计测功能设置为ON的话、就不需要这个编码处理了。）

* [Install计测的详细](./doc/track_install/README.md)

* [Deferred Deeplink的执行](./doc/deferred_deeplink/README.md)

<div id="tracking_reengagement"></div>

## 5. 流失唤回广告(Reengagement)计测的安装

为进行流失唤回广告的计测（计测经由URL SCHEME的启动行为）,需在URL SCHEME的全部`Activity`的onResume()中安装[`Fox.trackDeeplinkLaunch`](./doc/sdk_api/README.md#foxconfig)方法。

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
	// 流失唤回广告计测
	Fox.trackDeeplinkLaunch(this);
}
```

> ※ 考虑到URL SCHEME启动的Activity的launchMode为"singleTask"或"singleInstance"的情况，为接受最新Intent，请重写`onNewIntent`方法，并调用`setIntent`方法。
>
> ※ 流失唤回广告计测时，必须确认定义为AndroidManifest.xml的Acitvity中已经设置了[自定义URL SCHEME](#setting_urlscheme)。该计测是通过自定义URL SCHEME调用Activity来进行流失唤回广告的计测。
>
> ※ 从SDK4.3.0版本开始这部分的代码就可以省略了。（将自动计测功能设置为ON的话、就不需要这个编码处理了。）

<div id="tracking_event"></div>

## 6.  APP内事件(Event)计测

* [事件计测的详细](./doc/track_events/README.md)

<div id="tracking_session"></div>

### 6.1 Session（启动事件）的计测

可以计测自然流量和广告流量的安装数对比、APP启动次数和UU数（DAU/MAU)、留存率等。如不需要流量分析，可以忽略本项。

为了在APP启动或从后台恢复到前台时做计测，需在各Activity的onResume方法、以及基本Acitvity的onResume方法中执行[`Fox.trackSession方法`](./doc/sdk_api/README.md#fox)。

※请在进行[Install计测](#tracking_install)之后再调用Session（启动事件）的计测处理。

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

> ※ 从SDK4.3.0版本开始这部分的代码就可以省略了。（将自动计测功能设置为ON的话、就不需要这个编码处理了。）

**[安装到APP内所有的Activity]**

[上面的激活章节](#activate_sdk_into_app)中介绍过，Application继承类中执行以下内容，可以节省所有Activity的onResume方法中安装计测处理的时间。
但是，使用[`registerActivityLifecycleCallbacks`](https://developer.android.com/reference/android/app/Application.html#registerActivityLifecycleCallbacks(android.app.Application.ActivityLifecycleCallbacks))时候、APP的minSdkVersion需要14及以上。


```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // 激活处理
        private int FOX_APP_ID = 发行的APP ID;
        private String FOX_APP_KEY = "发行的APP_KEY";
        private String FOX_APP_SALT = "发行的APP_SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // APP的生命周期的检查
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

### 6.2 其他的APP内事件(Event)计测

在会员注册，完成新手引导，付费等任意成果地点执行事件计测，能够测定广告流入源的LTV。<br>

※请在进行[Install计测](#tracking_install)和[Session(起动事件)](#tracking_session)的计测处理之后再调用APP内事件的计测处理。

<div id="tracking_event_tutorial"></div>
**[新手引导事件的计测实例]**

```java
import co.cyberz.fox.service.FoxEvent;
import co.cyberz.fox.Fox;

 ...

FoxEvent tutorialEvent = new FoxEvent("_tutorial_comp", 成果地点ID);
tutorialEvent.buid = "用户ID";
Fox.trackEvent(tutorialEvent);
```

> 进行事件计测时、需指定识别成果地点的`成果地点ID`。[`FoxEvent`](./doc/sdk_api/README.md#foxevent) 类的构造函数的参数中请指定事件名称和生成的ID.

<div id="tracking_event_purchase"></div>

**[付费事件的计测案例]**

进行付费计测时，请在付费完成的位置指定付费金额和货币代码。

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

币种（currency）中请指定[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)认证的货币代码。

[事件计测的详细](./doc/track_events/README.md)

<div id="quickly_integration"></div>

## 7. 最简单的实际安装案例

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;

通过执行下面的案例代码，相当于执行了下面4个部分的代码处理。

* 激活F.O.X SDK
* 首次启动时的Install计测
* Session事件的计测
* 流失唤回广告计测

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 发行的APPID, appKey = "发行的APP_KEY", appSalt = "发行的APP_SALT")
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDK激活、自动计测的开始
        Fox.AUTOMATOR.init(this).startTrack();
    }
}
```

※ 上述安装中进行Cookie计测，浏览器自动跳转至APP时，请在AndroidManifest里设置主要的Activity自定义URL SCHEME。


<div id="other_function"></div>

## 8. 安装其他功能

* [安装Optout](./doc/optout/README.md)

* [利用外部储存优化重复排除](./doc/external_storage/README.md)

* [使用自动备份功能 Android M](./doc/auto_backup/README.md)

<div id="trouble_shooting"></div>

## 9. 最后需确认内容（常见问题集）

* [FAQ・注意事项](./doc/trouble_shooting/README.md)

<div id="sdk_api"></div>

## 10. SDK API

* [SDK API](./doc/sdk_api/README.md)
