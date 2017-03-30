# Force Operation Xとは

Force Operation X (以下F.O.X)は、スマートフォンにおける広告効果最適化のためのトータルソリューションプラットフォームです。アプリケーションのダウンロード、ウェブ上でのユーザーアクションの計測はもちろん、スマートフォンユーザーの行動特性に基づいた独自の効果計測基準の元、企業のプロモーションにおける費用対効果を最大化することができます。

本ドキュメントでは、スマートフォンアプリケーションにおける広告効果最大化のためのF.O.X SDK導入手順について説明します。

## 目次

* **[1. インストール](#install_sdk)**
	* [SDKダウンロード](https://github.com/cyber-z/public_fox_android_sdk/releases)
	* [AndroidStudioプロジェクトへの導入の方法](./doc/integration/android_studio/README.md)
	* [Eclipseプロジェクトへの導入の方法](./doc/integration/eclipse/README.md)
* **[2. 設定](#setting_sdk)**
* **[3. インストール計測の実装](#tracking_install)**
	* [sendConversionの詳細](./doc/send_conversion/README.md)
	* [リエンゲージメント計測](#reengagement_tracking)
	* [ディファードディープリンクの実装](./doc/deferred_deeplink/README.md)
* **[4. LTV計測の実装](#tracking_ltv)**
	* [タグを利用したLTV計測について](./doc/ltv_browser/README.md)
* **[5. アクセス解析の実装](#tracking_analytics)**
	* [アクセス解析によるイベント計測](./doc/analytics_event/README.md)
	* [アクセス解析による課金計測](./doc/analytics_purchase/README.md)
	* [エンゲージメント配信について](./doc/fox_engagement/README.md)
* **[6. ProGuardを利用する場合](#use_proguard)**
* **[7. 疎通テストの実施](#integration_test)**
	* [リエンゲージメント計測を行う場合のテスト手順](./doc/reengagement_test/README.md)
* **[8. その他機能の実装](#other_function)**
	* [プッシュ通知の実装](./doc/notify/README.md)
	* [オプトアウトの実装](./doc/optout/README.md)
	* [広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/README.md)
	* [外部ストレージを利用した重複判定設定](./doc/external_storage/README.md)
	* [Android M(6.0) オートバックアップ機能の利用](./doc/auto_backup/README.md)
* **[9. 最後に必ずご確認ください](#trouble_shooting)**

## F.O.X SDKとは

F.O.X SDKをアプリケーションに導入することで、以下の機能を実現します。

* **インストール計測**

広告流入別にインストール数を計測することができます。

* **LTV計測**

流入元広告別にLife Time Valueを計測します。主な成果地点としては、会員登録、チュートリアル突破、課金などがあります。各広告別に登録率、課金率や課金額などを計測することができます。

* **アクセス解析**

自然流入と広告流入のインストール比較。アプリケーションの起動数やユニークユーザー数(DAU/MAU)。継続率等を計測することができます。

* **プッシュ通知**

F.O.Xで計測された情報を使い、ユーザーに対してプッシュ通知を行うことができます。例えば、特定の広告から流入したユーザーに対してメッセージを送ることができます。

* **広告配信**

アプリ内に相互集客広告を表示させることができます。尚、広告表示が不要の場合には、本項目の実装を省略できます。

<div id="install_sdk"></div>
## 1. インストール

プロジェクトのbuild.gradleに以下を追加してください。

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

手動でインストールを行う場合は、以下のページより最新のSDKをダウンロードしてください。

* [SDKリリースページ](https://github.com/cyber-z/public_fox_android_sdk/releases)

ダウンロードしたSDKを展開し、`libs/AppAdForce_{VERSION}.jar`をアプリケーションのプロジェクトに組み込んでください。

**[詳細]**
* [AndroidStudioプロジェクトへの導入の方法](./doc/integration/android_studio/README.md)
* [Eclipseプロジェクトへの導入の方法](./doc/integration/eclipse/README.md)

> ※ 既にアプリケーションにSDKが導入されている場合には、[最新バージョンへのアップデートについて](./doc/update/README.md)をご参照ください。

<div id="setting_sdk"></div>
## 2. 設定


* **SDK設定**

SDKの動作に必要な設定をAndroidManifest.xmlに追加します。

### パーミッションの設定

F.O.X SDKでは下記4つのパーミッションを利用します。
&lt;Manifest&gt;タグ内に次のパーミッションの設定を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

パーミッション|Protection Level|必須|概要
:---|:---:|:---:|:---
INTERNET|Normal|必須|F.O.X SDKが通信を行うために必要となります。
ACCESS_NETWORK_STATE|Normal|必須|F.O.X SDKが通信可能かを確認するために必要となります。
READ_EXTERNAL_STORAGE ※1|Dangerous|任意|ストレージを利用した重複判定精度向上に必要となります。(※2)
WRITE_EXTERNAL_STORAGE ※1|Dangerous|任意|ストレージを利用した重複判定精度向上に必要となります。(※2)

> ※1 READ_EXTERNAL_STORAGE及びWRITE_EXTERNAL_STORAGEパーミッションは、外部ストレージにデータを記録することでアプリの再インストール時により正確にインストール計測を行うために必要ですが、必須ではありません。

> ※2 Android MよりProtectionLevelが`dangerous`に指定されているパーミッションを必要とする機能を利用するには、ユーザーの許可が必要になります。詳細は[外部ストレージを利用した重複判定設定](./doc/external_storage/README.md)をご確認ください。

### メタデータの設定

SDKの実行に必要な情報を<application>タグ内に追加します。

```xml
<meta-data
	android:name="APPADFORCE_APP_ID"
	android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data
	android:name="APPADFORCE_SERVER_URL"
	android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data
	android:name="APPADFORCE_CRYPTO_SALT"
	android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data
	android:name="ANALYTICS_APP_KEY"
	android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
```

設定するキーとバリューは以下の通りです。

|パラメータ名|必須|概要|
|:------|:------|:------|
|APPADFORCE_APP_ID|必須|Force Operation X管理者より連絡しますので、その値を入力してください。|
|APPADFORCE_SERVER_URL|必須|Force Operation X管理者より連絡しますので、その値を入力してください。|
|APPADFORCE_CRYPTO_SALT|必須|Force Operation X管理者より連絡しますので、その値を入力してください。|
|ANALYTICS_APP_KEY|必須|Force Operation X管理者より連絡しますので、その値を入力してください。|


### インストールリファラ計測の設定
インストールリファラーを用いたインストール計測を行うために下記の設定を<application>タグに追加します。

```xml
<receiver android:name="jp.appAdForce.android.InstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

既に"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスが定義されている場合には、[二つのINSTALL_REFERRERレシーバーを共存させる場合の設定](./doc/install_referrer/README.md)をご参照ください。


### URLスキームの設定

アプリを外部から起動できるようにするため、起動させる対象の&lt;activity&gt;タグ内に下記の設定を追加してください。

```xml
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
	<data android:scheme="任意のURLスキームを記入します" />
</intent-filter>
```

> 環境によっては、URLスキームの大文字小文字が判別されないことにより正常に URLスキームの遷移が行えない場合があります。URLスキームは全て小文字の英数字を用いて設定を行ってください。

### 広告IDを利用するためのGoogle Play Services SDKの導入

広告IDを利用するためにはGoogle Play Services SDKが導入されている必要があります。Google Play Services SDKが導入されていない場合でもF.O.X SDKは動作しますが、広告IDが利用されないことで一部広告メニューの計測が行えなかったり、重複判定の精度が低下いたします。導入の詳細手順は[広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/README.md)をご参照ください。

### AndroidManifest.xmlサンプル

[AndroidManifest.xmlサンプル](./doc/config_android_manifest/AndroidManifest.xml)


<div id="tracking_install"></div>
## 3. インストール計測の実装

初回起動のインストール計測を実装することで、広告の効果測定を行うことができます。プロジェクトのソースコードを編集し、次の通り実装を行ってください。

アプリケーションの起動時に呼び出されるActivityのonCreate()内にsendConversionメソッドを実装します。

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);

	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}
```

sendConversionの引数には、通常は上記の通り"default"という文字列をそのまま指定してください。<br>

* [sendConversionの詳細](./doc/send_conversion/README.md)
* [ディファードディープリンクの実装](./doc/deferred_deeplink/README.md)

<div id="reengagement_tracking"></div>
### リエンゲージメント計測

リエンゲージメント広告の計測（URLスキーム経由の起動を計測）するために、`URLスキームが設定されている全てのActivity(※1)`のonResume()に`sendDeeplinkConversion`メソッドを実装します。

[![F.O.X](http://img.shields.io/badge/Version-3.5.0%20+-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/blob/master/4.x/lang/ja/README.md)

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendDeeplinkConversion(getIntent());
}
```

> ※ SDKバージョン3.4.0まではリエンゲージメント計測に`sendReengagementConversion`メソッドを実装していましたが<br>
バージョン3.5.0より[ディファードディープリンク](./doc/deferred_deeplink/README.md)のサポートに伴い`sendDeeplinkConversion`メソッドを実装します。

> ※ バージョン3.7.0より`sendDeeplinkConversion`の第二引数にBuid(広告主端末ID(アプリ内でのユーザーID等))を指定することで、リエンゲージメント計測でもBuidを計測することが可能となっています。

---

[![F.O.X](http://img.shields.io/badge/Version-〜%203.4.0-blue.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/blob/master/4.x/lang/ja/README.md)
[![F.O.X](http://img.shields.io/badge/3.5.0+-Depricated-red.svg?style=flat)](#reengagement_tracking)

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendReengagementConversion(getIntent());
}
```

> SDKバージョン3.5.0以降、`sendReengagementConversion`メソッドはDepricatedとなっておりますのでご注意ください。

---

[![F.O.X](http://img.shields.io/badge/Version-ALL-yellow.svg?style=flat)](https://github.com/cyber-z/public-fox-android-sdk/blob/master/4.x/lang/ja/README.md)

URLスキームで起動されるActivityのlaunchModeが"singleTask"または"singleInstance"の場合は、URLスキーム経由でパラメータを受け取るために`onNewIntent`メソッドをoverrideし、以下のように`setIntent`メソッドをコールしてください。

```java
@Override
protected void onNewIntent(Intent intent)
{
	super.onNewIntent(intent);
	setIntent(intent);
}
```

> ※1 リエンゲージメント広告の計測を行うためにはAndroidManifest.xmlに定義されているAcitvityにカスタムURLスキームが設定されている必要があります。本計測ではカスタムURLスキームによってActivityが呼び出されることでリエンゲージメント計測を行います。

<div id="tracking_ltv"></div>
## 4. LTV計測の実装

会員登録、チュートリアル突破、課金など任意の成果地点にLTV計測を実装することで、流入元広告のLTVを測定することができます。LTV計測が不要の場合には、本項目の実装を省略できます。

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(成果地点ID);
```

LTV計測を行うためには、各成果地点を識別する成果地点IDを指定する必要があります。`sendLtvConversion`メソッドの引数に発行されたIDを指定してください。

課金計測を行う場合には、課金が完了した箇所で以下のように課金額と通貨コードを指定してください。

```java
import jp.appAdForce.android.LtvManager;
// ...
LtvManager ltv = new LtvManager(ad);
ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
ltv.sendLtvConversion(成果地点ID);
```

LtvManager.URL_PARAM_CURRENCYには[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)で定義された通貨コードを指定してください。

* [sendLtvConversionの詳細](./doc/send_ltv_conversion/README.md)
* [タグを利用したLTV計測について](./doc/ltv_browser/README.md)

<div id="tracking_analytics"></div>
## 5. アクセス解析の実装

自然流入と広告流入のインストール数比較、アプリケーションの起動数やユニークユーザー数(DAU/MAU)、継続率等を計測することができます。アクセス解析が不要の場合には、本項目の実装を省略できます。

アプリケーションの起動、及びバックグラウンドからの復帰を計測するために、各ActivityのonResume()に`AnalyticsManager.sendStartSession`メソッドを追加します。

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

> ※アプリケーションが複数の Activity を生成する場合には、全てのActivityの onResume()に処理を 追加してください。アプリケーションがバックグラウンドから復帰した際に、その Activity に起動計測の実装がされていない場合など、正確なアクティブユーザー数が計測できなくなります。

[アクセス解析によるイベント計測](./doc/analytics_event/README.md)

[アクセス解析による課金計測](./doc/analytics_purchase/README.md)

[エンゲージメント配信について](./doc/fox_engagement/README.md)

<div id="use_proguard"></div>
## 6. ProGuardを利用する場合

ProGuard を利用してアプリケーションの難読化を行う際は F.O.X SDK のメソッドが対象とならないよう、以下の設定 を追加してください。

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

また、Google Play Service SDK を導入されている場合は、以下のページで記載されている keep 指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)

<div id="integration_test"></div>
## 7. 疎通テストの実施

マーケットへの申請までに、SDKを導入した状態で十分にテストを行い、アプリケーションの動作に問題がないことを確認してください。

インストール計測の通信は、起動後に一度のみ行わるため、続けて効果測定テストを行いたい場合には、アプリケーションをアンインストールし、再度インストールから行ってください。

* **テスト手順**

1. テスト用端末にテストアプリがインストールされている場合には、アンインストール
1. テスト用端末のデフォルトブラウザのCookieを削除
1. 弊社より発行したテスト用URLをクリック
1. マーケットへリダイレクト
1. テスト用端末にテストアプリをインストール<br />
1. アプリを起動、ブラウザが起動<br />
ここでブラウザが起動しない場合には、正常に設定が行われていません。設定を見直していただき、問題が見当たらない場合には弊社へご連絡ください。
1. LTV地点まで画面遷移<br />
1. アプリを終了し、バックグラウンドからも削除<br />
1. 再度アプリを起動<br />

弊社へ3、6、7、9の時間をお伝えください。正常に計測が行われているか確認いたします。弊社側の確認にて問題がなければテスト完了となります。

> テスト用URLは必ず端末のデフォルトブラウザ上でリクエストされるようにしてください。メールアプリやQRコードアプリを利用されそのアプリ内WebViewで遷移した場合には計測できません。

> テストURLをクリックした際に、遷移先がなくエラーダイアログが表示される場合がありますが、疎通テストにおいては問題ありません。


[リエンゲージメント計測を行う場合のテスト手順](./doc/reengagement_test/README.md)

<div id="other_function"></div>
## 8. その他機能の実装

* [プッシュ通知の実装](./doc/notify/README.md)

* [オプトアウトの実装](./doc/optout/README.md)

<div id="trouble_shooting"></div>
## 9. 最後に必ずご確認ください（これまで発生したトラブル集）

### 9.1. URLスキームの設定がされずリリースされたためブラウザからアプリに遷移ができない

Cookie 計測を行いブラウザを起動した場合には、URL スキームを利用してアプリケーションに遷移します。 この際、独自の URL スキームが設定されている必要があります。


### 9.2. URLスキームに大文字が含まれ、正常にアプリに遷移されない

環境によって、URLスキームの大文字小文字が判別されないことにより正常に URLスキームの遷移が行えない場合があります。URLスキームは全て小文字で設定を行ってください。


### 9.3. F.O.Xで確認できるインストール数の値がGoogle Play Developer Consoleの数字より大きい

F.O.Xではいくつかの方式を組み合わせて端末の重複インストール検知を行っています。
重複検知が行えない設定では、同一端末でも再インストールされる度にF.O.Xは新規のインストールと判定してしまいます。

重複検知の精度を向上するために、以下の設定を行ってください。

* [広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/README.md)

* [外部ストレージを利用した重複判定設定](./doc/external_storage/README.md)

* [Android M(6.0) オートバックアップ機能の利用](./doc/auto_backup/README.md)
