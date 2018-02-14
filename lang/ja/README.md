[![Language](http://img.shields.io/badge/language-java-red.svg?style=flat)](https://java.com)
[![Platform](http://img.shields.io/badge/platform-Android-green.svg?style=flat)](https://developer.android.com)
[![Android](http://img.shields.io/badge/support-API_Level_14+-green.svg?style=flat)](https://developer.android.com)

# Force Operation Xとは

Force Operation X (以下F.O.X)は、スマートフォンにおける広告効果最適化のためのトータルソリューションプラットフォームです。アプリケーションのダウンロード、ウェブ上でのユーザーアクションの計測はもちろん、スマートフォンユーザーの行動特性に基づいた独自の効果計測基準の元、企業のプロモーションにおける費用対効果を最大化することができます。

本ドキュメントでは、スマートフォンアプリケーションにおける広告効果最大化のためのF.O.X SDK導入手順について説明します。

## 目次

* **[F.O.X SDKとは](#whats_fox_sdk)**
* **[1. インストール](#install_sdk)**
	* [SDKダウンロード](https://github.com/cyber-z/public_fox_android_sdk/releases)
	* [AndroidStudioプロジェクトへの導入の方法](./doc/integration/android_studio/README.md)
	* [Eclipseプロジェクトへの導入の方法](./doc/integration/eclipse/README.md)
	* [旧バージョンからのマイグレーション](./doc/migration/README.md)
* **[2. 設定](#setting_sdk)**
	* [2.1 パーミッションの設定](#setting_permission)
	* [2.2 Proguardの設定](#setting_proguard)
	* [2.3 インストールリファラ計測の設定](#setting_installreferrer)
	* [2.4 カスタムURLスキームの設定](#setting_urlscheme)
	* [2.5 広告IDを利用するためのGoogle Play Servicesの導入](#setting_googleplayservices)
	* [SDK API](./doc/sdk_api/README.md)
* **[3. F.O.X SDKのアクティベーション](#activate_sdk_into_app)**
	* [自動計測の詳細](./doc/track_auto/README.md)
* **[4. インストール計測の実装](#tracking_install)**
	* [インストール計測の詳細](./doc/track_install/README.md)
	* [ディファードディープリンクの実装](./doc/deferred_deeplink/README.md)
* **[5. リエンゲージメント計測の実装](#tracking_reengagement)**
* **[6. アプリ内イベントの計測](#tracking_event)**
	* [6.1 セッション（起動イベント）の計測](#tracking_session)
	* [6.2 その他のアプリ内イベントの計測](#tracking_other_event)
	* [イベント計測の詳細 (エンゲージメント計測・ダイナミック配信連携..)](./doc/track_events/README.md)
* **[7. 最短実装の例](#quickly_integration)**
* **[8. その他機能の実装](#other_function)**
	* [オプトアウトの実装](./doc/optout/README.md)
	* [外部ストレージを利用した重複排除設定](./doc/external_storage/README.md)
	* [オートバックアップ機能の利用 (Android M)](./doc/auto_backup/README.md)
* **[9. 最後に必ずご確認ください](#trouble_shooting)**
	* [FAQ・注意事項](./doc/trouble_shooting/README.md)


<div id="whats_fox_sdk"></div>

## F.O.X SDKとは

F.O.X SDKをアプリケーションに導入することで、以下の機能を実現します。

* **インストール計測**

広告流入別にインストール数を計測することができます。

* **LTV計測**

流入元広告別にLife Time Valueを計測します。主な成果地点としては、会員登録、チュートリアル突破、課金などがあります。各広告別に登録率、課金率や課金額などを計測することができます。

* **アクセス解析**

自然流入と広告流入のインストール比較。アプリケーションの起動数やユニークユーザー数(DAU/MAU)。継続率等を計測することができます。

<div id="install_sdk"></div>

## 1. インストール

F.O.X Android SDK 4.0.0〜 は `Android 4.0(API Level 14)` 以上をサポートしています。

F.O.X SDKモジュールをGradleを用いてインポートする場合、以下の設定をプロジェクトのbuild.gradleに追加してください。

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

上記、dependenciesに指定するSDKバージョンは以下のSDKリリースページでご確認ください。<br>
また、手動でインストールを行う場合は、リリースページより最新のSDKをダウンロードしてください。

* [SDKリリースページ](https://github.com/cyber-z/public_fox_android_sdk/releases)

ダウンロードしたSDKを展開し、`libs/FOX_Android_SDK_{VERSION}.jar`をアプリケーションのプロジェクトに組み込んでください。

**[詳細]**
* [AndroidStudioプロジェクトへの導入の方法](./doc/integration/android_studio/README.md)
* [Eclipseプロジェクトへの導入の方法](./doc/integration/eclipse/README.md)

> ※ 既にアプリケーションにSDKが導入されている場合には、[最新バージョンへのアップデートについて](./doc/migration/README.md)をご参照ください。

<div id="setting_sdk"></div>

## 2. 設定

* **SDK設定**

SDKの動作に必要な設定をAndroidManifest.xmlに追加します。

<div id="setting_permission"></div>

### 2.1 パーミッションの設定

F.O.X SDKでは下記3つのパーミッションを利用します。
&lt;Manifest&gt;タグ内に次のパーミッションの設定を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

パーミッション|Protection Level|必須|概要
:---|:---:|:---:|:---
INTERNET|Normal|必須|F.O.X SDKが通信を行うために必要となります。
WRITE_EXTERNAL_STORAGE ※1|Dangerous|任意|ストレージを利用した重複排除機能向上に必要となります。(※2)

> ※1 READ_EXTERNAL_STORAGE及びWRITE_EXTERNAL_STORAGEパーミッションは、外部ストレージにデータを記録することでアプリの再インストール時により正確にインストール計測を行うために必要ですが、必須ではありません。

> ※2 Android MよりProtectionLevelが`dangerous`に指定されているパーミッションを必要とする機能を利用するには、ユーザーの許可が必要になります。詳細は[外部ストレージを利用した重複排除設定](./doc/external_storage/README.md)をご確認ください。

<div id="setting_proguard"></div>

### 2.2 Proguardの設定
Proguard を利用してアプリケーションの難読化を行う際は F.O.X SDK のメソッドが対象とならないよう、以下の設定 を追加してください。

```
-keepattributes *Annotation*
-keep class co.cyberz.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn co.cyberz.**
# Gradle経由でSDKをインストールしている場合、下記jarファイルの指定は不要です。
-libraryjars libs/FOX_Android_SDK_{VERSION}.jar
```

また、Google Play Service SDK を導入されている場合は、以下のページで記載されている keep 指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)

<div id="setting_installreferrer"></div>

### 2.3 インストールリファラ計測の設定
インストールリファラーを用いたインストール計測を行うために下記の設定を&lt;application&gt;タグに追加します。

```xml
<receiver android:name="co.cyberz.fox.FoxInstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

#### Google Play Referrer APIを用いる場合

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.4.0%20〜-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/releases/tag/4.4.1)

以下の[Google Play Referrer API](https://developer.android.com/google/play/installreferrer/library.html)のdependencyをbuild.gradleに追加することで本API経由でのInstallReferrer計測が可能となります。<br>
(得にSDKの実装は必要ありません)

```
dependencies {
    compile 'com.android.installreferrer:installreferrer:1.0'
}
```

既に"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスが定義されている場合には、[複数のINSTALL_REFERRERレシーバーを共存させる場合の設定](./doc/install_referrer/README.md)をご参照ください。

<div id="setting_urlscheme"></div>

### 2.4 カスタムURLスキームの設定

アプリを外部から起動できるようにするため、起動させる対象の&lt;activity&gt;タグ内に下記の設定を追加してください。<br>

```xml
<activity
    android:name=".FirstActivity"
    android:screenOrientation="portrait"
    android:launchMode="singleTop">
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>

    <!-- アプリを起動するためのカスタムURLスキーム設定 -->
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="任意のURLスキームを記入します" />
    </intent-filter>
</activity>
```

<br>主要な例として以下が挙げられます。

* Cookie計測を行う場合に、ブラウザからアプリ戻る際のActivityに設定
* [リエンゲージメント計測](#tracking_reengagement)を行うActivityに設定

> ※ Cookie計測で起動するブラウザからアプリに戻す際、指定するカスタムURLスキームは、[Cookie計測の実装](#tracking_install)を行っているActivityを指定することを推奨します。

> ※ Cookie計測やリエンゲージメント計測を行う際のActivityの`launchMode`は`singleTop`を推奨します。

> ※ 環境によっては、URLスキームの大文字小文字が判別されないことにより正常に URLスキームの遷移が行えない場合があります。URLスキームは全て小文字の英数字を用いて設定を行ってください。

> ※ カスタムURLスキームの詳細は[Android Developers (暗黙的インテントの受信) ](https://developer.android.com/guide/components/intents-filters.html#Receiving)をご確認ください。

<div id="setting_googleplayservices"></div>

### 2.5 広告IDを利用するためのGoogle Play Servicesの導入

広告IDを利用するためにはGoogle Play Servicesが導入されている必要があります。<br>
Gradleで導入する場合、導入先のアプリの`build.gradle`の`dependencies`に以下の設定を追加します。

```
	compile 'com.google.android.gms:play-services:9.4.0'
```

広告IDを取得するためだけにGooglePlayServicesを導入される場合、以下の最小指定で構いません。

```
	compile 'com.google.android.gms:play-services-ads:9.4.0'
```

> Google Play Services SDKが導入されていない場合でもF.O.X SDKは動作しますが、広告IDが利用されないことで一部広告メニューの計測が行えなかったり、重複判定の精度が低下致します。<br>導入の詳細手順は[広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/README.md)をご参照ください。



### AndroidManifest.xmlサンプル

[AndroidManifest.xmlサンプル](./doc/config_android_manifest/AndroidManifest.xml)


<div id="activate_sdk_into_app"></div>

## 3. F.O.X SDKのアクティベーション

F.O.X SDKのアクティベーションを行うため、[`FoxConfig`](./doc/sdk_api/README.md#foxconfig)クラスをApplicationクラスを継承したクラスのonCreateメソッド内に実装します。<br>
また、Applicationを継承しているクラスはAndroidManifest.xmlのapplicationタグのname属性に指定する必要があります。

**AndroidManifestへのApplication継承クラスの設定例**

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

**Application継承クラスの実装**

<div id="new_activation"></div>

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;アクティベーション兼、自動計測の実行&nbsp;]


```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDKアクティベーション、自動計測開始
        Fox.AUTOMATOR.init(this).startTrack();
    }
}

```
> ※ appId、salt、appKeyの値については、アプリ登録後、F.O.X管理画面のアプリ一覧>該当アプリ右上の設定ボタン>SDK導入をご確認ください。

> 上記のコードはアクティベーションと自動計測の両方を実行しています。既に従来のバージョンが導入済みの場合、必ず[自動計測の詳細](./doc/track_auto/README.md)をご確認ください。

* [自動計測の詳細](./doc/track_auto/README.md)

<div id="old_activation"></div>

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-〜%204.2.1-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/releases/tag/4.2.1)&nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;アクティベーションの実行&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.FoxConfig;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        int FOX_APP_ID = 発行されたアプリID;
        String FOX_APP_KEY = "発行されたAPP_KEY";
        String FOX_APP_SALT = "発行されたAPP_SALT";
        FoxConfig config = new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT);
				  config.addDebugOption(BuildConfig.DEBUG)
						.activate();
    }
}

```

<div id="tracking_install"></div>

## 4. インストール計測の実装

初回起動のインストール計測を実装することで、広告の効果測定を行うことができます。<br>
以下の[`Fox.trackInstall`](./doc/sdk_api/README.md#fox)メソッドをアプリケーションの起動時に呼び出されるActivityのonCreateメソッド内に実装します。<br>

```java
import co.cyberz.fox.Fox;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);
	// インストール計測
	Fox.trackInstall();
}
```

> ※ ２回目以降、trackInstallメソッドが呼び出されても動作することはありません。
>
> ※ trackInstallメソッドにはオプションを指定することも可能です。詳細は[インストール計測の詳細](./doc/track_install/README.md)をご確認ください。
>
> ※ SDKバージョン4.3.0から本実装は省略することが可能です。（自動計測機能をONにしている場合、不要です。）

* [インストール計測の詳細](./doc/track_install/README.md)

* [ディファードディープリンクの実装](./doc/deferred_deeplink/README.md)

<div id="tracking_reengagement"></div>

## 5. リエンゲージメント計測の実装

リエンゲージメント広告の計測（URLスキーム経由の起動を計測）するために、`URLスキームが設定されている全てのActivity`のonResume()に[`Fox.trackDeeplinkLaunch`](./doc/sdk_api/README.md#foxconfig)メソッドを実装します。

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
	// リエンゲージメント計測
	Fox.trackDeeplinkLaunch(this);
}
```

> ※ URLスキームで起動されるActivityのlaunchModeが"singleTask"または"singleInstance"である場合を考慮し、最新のIntentを受け取るために`onNewIntent`メソッドをoverrideし、`setIntent`メソッドをコールしてください。
>
> ※ リエンゲージメント広告の計測を行うためにはAndroidManifest.xmlに定義されているAcitvityに[カスタムURLスキーム](#setting_urlscheme)が設定されていることが前提となります。本計測ではカスタムURLスキームによってActivityが呼び出されることでリエンゲージメント計測を行います。
>
> ※ SDKバージョン4.3.0から本実装は省略することが可能です。（自動計測機能をONにしている場合、不要です。）

<div id="tracking_event"></div>

## 6. アプリ内イベントの計測

* [イベント計測の詳細](./doc/track_events/README.md)

<div id="tracking_session"></div>

### 6.1 セッション（起動イベント）の計測

自然流入と広告流入のインストール数比較、アプリケーションの起動数やユニークユーザー数(DAU/MAU)、継続率等を計測することができます。アクセス解析が不要の場合には、本項目の実装を省略できます。<br>

アプリケーションの起動、及びバックグラウンドからの復帰を計測するために、各ActivityのonResumeメソッド内または、ベースとなるAcitvityのonResumeメソッド内に[`Fox.trackSession`](./doc/sdk_api/README.md#fox)メソッドを実装します。

※セッション（起動イベント）の計測がコールされるタイミングは、[インストール計測](#tracking_install)の後になるように実装してください。

```java
import co.cyberz.fox.Fox;

public class BaseActivity extends Activity {

	@Override
	protected void onResume() {
		super.onResume();
		// セッショントラッキング
		Fox.trackSession();
	}
}
```

> ※ SDKバージョン4.3.0から本実装は省略することが可能です。（自動計測機能をONにしている場合、不要です。）

**[アプリケーション内の全てのActivityに実装する場合]**

[前述のアクティベーション](#activate_sdk_into_app)で紹介したApplication継承クラス内に以下を実装することで、全てのActivityのonResumeメソッドに計測処理を実装する手間を省くことができます。<br>
但し、[`registerActivityLifecycleCallbacks`](https://developer.android.com/reference/android/app/Application.html#registerActivityLifecycleCallbacks(android.app.Application.ActivityLifecycleCallbacks))を使用する場合、アプリのminSdkVersionが14以上である必要があります。

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // アクティベーション処理
        private int FOX_APP_ID = 発行されたアプリID;
        private String FOX_APP_KEY = "発行されたAPP_KEY";
        private String FOX_APP_SALT = "発行されたAPP_SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // アプリケーションのライフサイクルの検知
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
	      // セッショントラッキング
	      Fox.trackSession();
	    }
    ...
  }
}

```

<div id="tracking_other_event"></div>

### 6.2 その他のアプリ内イベントの計測

会員登録、チュートリアル突破、課金など任意の成果地点にイベント計測を実装することで、流入元広告のLTVを測定することができます。<br>イベント計測が不要の場合には、本項目の実装を省略できます。

※アプリ内イベントの計測がコールされるタイミングは、[インストール計測](#tracking_install)、[セッション（起動イベント）の計測](#tracking_session)の後になるように実装してください。

<div id="tracking_event_tutorial"></div>

**[チュートリアルイベントの計測例]**

```java
import co.cyberz.fox.service.FoxEvent;
import co.cyberz.fox.Fox;

 ...

FoxEvent tutorialEvent = new FoxEvent("_tutorial_comp", 成果地点ID);
tutorialEvent.buid = "ユーザーID";
Fox.trackEvent(tutorialEvent);
```

> イベント計測を行うためには、各成果地点を識別する`成果地点ID`を指定する必要があります。[`FoxEvent`](./doc/sdk_api/README.md#foxevent)クラスのコンストラクタの引数にイベント名と発行されたIDを指定してください。


<div id="tracking_event_purchase"></div>

**[課金イベントの計測例]**

課金計測を行う場合には、課金が完了した箇所で以下のように課金額と通貨コードを指定してください。

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

currencyの指定には[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)で定義された通貨コードを指定してください。

[イベント計測の詳細](./doc/track_events/README.md)

<div id="quickly_integration"></div>

## 7. 最短実装の例

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.3.0%20〜-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/releases/tag/4.3.0)&nbsp;&nbsp;&nbsp;

以下のサンプルコードを実装することで、以下4点の実装と同等の処理を実行します。

* F.O.X SDKのアクティベーション
* 初回起動時のインストール計測
* セッションイベントの計測
* リエンゲージメント計測

```java
import android.app.Application;
import co.cyberz.fox.Fox;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT")
public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // SDKアクティベーション、自動計測開始
        Fox.AUTOMATOR.init(this).startTrack();
    }
}

```

> ※ 上記の実装でCookie計測を行い、ブラウザからアプリに自動遷移させる際にはAndroidManifest上、mainとなるActivityのカスタムURLスキームをご連絡ください。


<div id="other_function"></div>

## 8. その他機能の実装

* [オプトアウトの実装](./doc/optout/README.md)

* [外部ストレージを利用した重複排除設定](./doc/external_storage/README.md)

* [オートバックアップ機能の利用 Android M](./doc/auto_backup/README.md)

<div id="trouble_shooting"></div>

## 9. 最後に必ずご確認ください（これまで発生したトラブル集）

* [FAQ・注意事項](./doc/trouble_shooting/README.md)
