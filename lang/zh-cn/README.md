# 什么是Force Operation X

Force Operation X（以下简称F.O.X）是一款用于优化智能手机广告效果的整体解决方案平台。不光是App的下载和网页用户行为的计测，基于智能用户行为特征的独特计测基准，能够实现企业推广的性价比最大化。

本文件是为实现智能手机APP中广告效果最大化的F.O.X SDK导入与安装的说明书。

## 目录

* **[什么是F.O.X SDK](#whats_fox_sdk)**
* **[1.导入](#install_sdk)**
	* [下载SDK](https://github.com/cyber-z/public_fox_android_sdk/releases)
	* [AndroidStudio项目的导入方法](./doc/integration/android_studio/README.md)
	* [Eclipse项目的导入方法](./doc/integration/eclipse/README.md)
	* [从旧版本转移](./doc/migration/README.md)
* **[2. 设置](#setting_sdk)**
	* [2.1 设置权限](#setting_permission)
	* [2.2 设置Proguard](#setting_proguard)
	* [2.3 设置Install Referrer计测](#setting_installreferrer)
	* [2.4 设置自定义URL SCHEME](#setting_urlscheme)
	* [2.5 为使用广告ID的Google Play Services导入](#setting_googleplayservices)
	* [SDK API](./doc/sdk_api/README.md)
* **[3. 激活F.O.X SDK](#activate_sdk_into_app)**
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


<div id="whats_fox_sdk"></div>

## 什么是F.O.X SDK

[![Language](http://img.shields.io/badge/language-java-red.svg?style=flat)](https://java.com)
[![Platform](http://img.shields.io/badge/platform-Android-green.svg?style=flat)](https://developer.android.com)
[![Android](http://img.shields.io/badge/support-API_Level_14+-green.svg?style=flat)](https://developer.android.com)


将F.O.X SDK导入APP之后，能够实现以下功能。

* **Install计测**

能够计测不同广告带来的安装数。

* **LTV计测**

可以计测不同广告来源的Life Time Value。主要的成果地点为会员注册、新手引导完成、付费等。能够分别监测各广告的登录率、付费率以及付费金额。

* **流量分析**

比较自然流入和广告流入带来的安装。能够计测App的启动次数和唯一用户数(DAU/MAU)、留存率等。

<div id="install_sdk"></div>

## 1. 导入

使用Gradle来导入F.O.X SDK module时，请将以下设置写入项目build.gradle。

```
repositories {
    maven {
        url "https://github.com/cyber-z/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    compile 'co.cyberz.fox:track-core:4.x.x'
}
```


dependencies中指定的SDK版本号与下面下载页面中一致。<br>
如果使用本地jar来安装的话，请从release页面下载相应版本的zip文件。

* [SDK下载](https://github.com/cyber-z/public_fox_android_sdk/releases)

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
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

权限|Protection Level|必須|概要
:---|:---:|:---:|:---
INTERNET|Normal|必須|F.O.X SDK进行通信的必要条件。
READ_EXTERNAL_STORAGE ※1|Dangerous|任意|使用外部存储来优化排除重复功能时必须设置。(※2)
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
	compile 'com.google.android.gms:play-services:9.4.0'
```

如果仅是为获取广告ID而导入GooglePlayServices，使用下面的最小指定就可以。

```
	compile 'com.google.android.gms:play-services-ads:9.4.0'
```

> 如果没有导入Google Play Services SDK却启动F.O.X SDK，会因为没有使用广告ID导致部分的广告菜单计测无法进行，或重复判定的准确率较低等问题。<br>
导入的具体步骤请参照[导入Google Play Services来使用广告ID](./doc/google_play_services/README.md)。


### AndroidManifest.xml实例

[AndroidManifest.xml实例](./doc/config_android_manifest/AndroidManifest.xml)


<div id="activate_sdk_into_app"></div>

## 3. 激活F.O.X SDK

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

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;

public class YourApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        int FOX_APP_ID = 发行的APP ID;
        String FOX_APP_KEY = "发行的APP_KEY";
        String FOX_APP_SALT = "发行的APP_SALT";
        FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
				  config.addDebugOption(BuildConfig.DEBUG)
						.activate();
    }
}

```


<div id="tracking_install"></div>

## 4. Install计测的安装
进行初次启动的Install计测，可以计测广告效果。<br>
在启动APP时调用的Activity的onCreate方法中安装下面的[`Fox.trackInstall`](./doc/sdk_api/README.md#fox)。<br>

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

> ※ 在trackInstall方法中可以指定option参数，具体请查看[Install计测的详细](./doc/track_install/README.md)。

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

> ※1 考虑到URL SCHEME启动的Activity的launchMode为"singleTask"或"singleInstance"的情况，为接受最新Intent，请重写`onNewIntent`方法，并调用`setIntent`方法。

> ※2 流失唤回广告计测时，必须确认定义为AndroidManifest.xml的Acitvity中已经设置了[自定义URL SCHEME](#setting_urlscheme)。该计测是通过自定义URL SCHEME调用Activity来进行流失唤回广告的计测。

<div id="tracking_event"></div>

## 6.  APP内事件(Event)计测

* [事件计测的详细](./doc/track_events/README.md)

<div id="tracking_session"></div>

### 6.1 Session（启动事件）的计测

可以计测自然流量和广告流量的安装数对比、APP启动次数和UU数（DAU/MAU)、留存率等。如不需要流量分析，可以忽略本项。

为了在APP启动或从后台恢复到前台时做计测，需在各Activity的onResume方法、以及基本Acitvity的onResume方法中执行[`Fox.trackSession方法`](./doc/sdk_api/README.md#fox)。

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
不需要事件计测的话，可以忽略本项。

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

* 激活F.O.X SDK
* 首次启动时的Install计测
* Session事件的计测
* 流失唤回广告计测

以下为同一位置中安装了可共通化处理的案例。其他各类事件，必须在每次发生时执行。

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

        // Application的生命周期的检查
        if (14 <= Build.VERSION.SDK_INT) {
        	registerActivityLifecycleCallbacks(new ApplicationLifeCycleCallbacks());
        }
    }


    private static final class ApplicationLifeCycleCallbacks implements ActivityLifecycleCallbacks {

	    @Override
	    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
	      // Install计测(※1)
	      Fox.trackInstall();
	    }

	    @Override
	    public void onActivityStarted(Activity activity) {
	    }

	    @Override
	    public void onActivityResumed(Activity activity) {
	      // Session Tracking
	      Fox.trackSession();
	      // 流失唤回广告计测
	      Fox.trackDeeplinkLaunch(activity);
	    }

	    @Override
	    public void onActivityPaused(Activity activity) {
	    }

	    @Override
	    public void onActivityStopped(Activity activity) {
	    }

	    @Override
	    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
	    }

	    @Override
	    public void onActivityDestroyed(Activity activity) {
	    }
  }
}
```

※1 使用[`registerActivityLifecycleCallbacks`](https://developer.android.com/reference/android/app/Application.html#registerActivityLifecycleCallbacks(android.app.Application.ActivityLifecycleCallbacks))时、APP的minSdkVersion需要大于或等于14。

※2 上述执行中使用Cookie追踪进行Install计测的情况时，APP的首次调用Activity时会启动浏览器。因此，请在首次Activity动作无误的情况时使用。<br>
进行InstallReferrer计测和Fingerprint计测时有效。

※3 上述仅为计测启动类事件的实际安装案例，新手引导完成・付费及账号注册等事件需要在其他Activity内执行计测。

※4 上述安装中进行Cookie计测，浏览器自动跳转至APP时，请在AndroidManifest里设置主要的Activity自定义URL SCHEME。


<div id="other_function"></div>

## 8. 安装其他功能

* [安装Optout](./doc/optout/README.md)

* [利用外部储存优化重复排除](./doc/external_storage/README.md)

* [使用自动备份功能 Android M](./doc/auto_backup/README.md)

<div id="trouble_shooting"></div>

## 9. 最后需确认内容（常见问题集）

### 9.1. 未设置URL SCHEME 进行发布后无法从浏览器跳转至APP

进行Cookie计测启动浏览器时，为了使用URL SCHEME迁移到Application需要设置URL SCHEME。

### 9.2. URL SCHEME中含有大写字母时，无法正常迁移到APP。

根据运行环境，会出现因为URL SCHEME 的大小写字母不能判定而导致使用URL SCHEME无法正常迁移的情况。请将URL SCHEME全部设置为半角小写英文数字或小数点。

### 9.3. F.O.X中计测的Install数值会大于Google Play Developer Console的数字

F.O.X结合多种方式来进行终端重复安装的检查。当设置无法进行检查重复时，同一终端的再次安装可能会被F.O.X判定为新的安装。

为提高排查重复的精度，请进行下面的设置。

* [导入Google Play Services SDK来使用广告ID](./doc/google_play_services/README.md)

* [利用外部储存优化重复排除](./doc/external_storage/README.md)

* [使用自动备份功能 Android M](./doc/auto_backup/README.md)
