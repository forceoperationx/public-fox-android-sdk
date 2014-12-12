# Force Opetaion Xとは

Force Operation X (以下F.O.X)は、スマートフォンにおける広告効果最適化のためのトータルソリューションプラットフォームです。アプリケーションのダウンロード、ウェブ上でのユーザーアクションの計測はもちろん、スマートフォンユーザーの行動特性に基づいた独自の効果計測基準の元、企業のプロモーションにおける費用対効果を最大化することができます。

本ドキュメントでは、スマートフォンアプリケーションにおける広告効果最大化のためのF.O.X SDK導入手順について説明します。

？ここで目次があると嬉しい。

## F.O.X SDKとは

F.O.X SDKをアプリケーションに導入することで、以下の機能を実現します。

* **インストール計測**

広告流入別にインストール数を計測することができます。

* **LTV計測**

流入元広告別にLife Time Valueを計測します。主な成果地点としては、会員登録、チュートリアル突破、課金などがあります。各広告別に登録率、課金率や課金額などを計測することができます。

？リエンゲージメント計測は不要？

* **アクセス解析**

自然流入と広告流入のインストール比較。アプリケーションの起動数やユニークユーザー数(DAU/MAU)。継続率等を計測することができます。

* **プッシュ通知**

F.O.Xで計測された情報を使い、ユーザーに対してプッシュ通知を行うことができます。例えば、特定の広告から流入したユーザーに対してメッセージを送ることができます。

## 1. インストール

以下のページより最新のSDKをダウンロードしてください。

[SDKリリースページ](https://github.com/cyber-z/public_fox_android_sdk/releases)

？SDKリリースページのリンク先だと、このドキュメントのリリースバージョンを管理するページになってしまうのですが、SDKのZIPファイルとかを別途管理するようなイメージでいいのかな？

既にアプリケーションにSDKが導入されている場合には、[最新バージョンへのアップデートについて](./doc/update/ja)をご参照ください。

ダウンロードしたSDK「FOX_Android_SDK_<version>.zip」を展開し、「AppAdForce.jar」をアプリケーションのプロジェクトに組み込んでください。

[Eclipseプロジェクトへの導入の方法](./doc/integration/eclipse/ja/)


[AndroidStudioプロジェクトへの導入の方法](./doc/integration/android_studio/ja/)



## 2. 設定


* **SDK設定**

SDKの動作に必要な設定をAndroidManifest.xmlに追加します。

？ここに↓のほうにあるManifest.xmlのサンプルリンクがあったほうがよさそう

### パーミッションの設定

<Manifest>タグ内に次のパーミッションの設定を追加します。

```xml:
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

### メタデータの設定

SDKの実行に必要な情報を<application>タグ内に追加します。

```xml:
<meta-data android:name="APPADFORCE_APP_ID" android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data android:name="APPADFORCE_SERVER_URL" android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data android:name="APPADFORCE_CRYPTO_SALT" android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
<meta-data android:name="ANALYTICS_APP_KEY" android:value="Force Operation X管理者より連絡しますので、その値を入力してください。" />
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

```xml:
<receiver android:name="jp.appAdForce.android.InstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

既に"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスが定義されている場合には、[二つのINSTALL_REFERRERレシーバーを共存させる場合の設定](./doc/install_referrer/ja/)をご参照ください。


### URLスキームの設定

アプリを外部から起動できるようにするため、起動させる対象の<activity>タグ内に下記の設定を追加してください。

```xml:
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
	<data android:scheme="任意のURLスキームを記入します" />
</intent-filter>
```

> 環境によっては、URLスキームの大文字小文字が判別されないことにより正常に URLスキームの遷移が行えない場合があります。URLスキームは全て小文字の英数字を用いて設定を行ってください。

[広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/ja/)

[（オプション）外部ストレージを利用した重複排除設定](./doc/external_storage/ja/)

[AndroidManifest.xmlサンプル](./doc/config_android_manifest/AndroidManifest.xml)


## 3. インストール計測の実装

初回起動のインストール計測を実装することで、広告の効果測定を行うことができます。プロジェクトのソースコードを編集し、次の通り実装を行ってください。

アプリケーションの起動時に呼び出されるActivityのonCreate()内にsendConversionメソッドを実装します。

```java:
import jp.appAdForce.android.AdManager;

onCreate() {
	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}

```

sendConversionの引数には、通常は上記の通り"default"という文字列を入力してください。

[sendConversionの詳細](./doc/send_conversion/ja/)

また、URLスキーム経由の起動を計測するために、URLスキームが設定されているActivityのonResume()にsendReengageConversionメソッドを実装します。


```java:
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendReengageConversion(getIntent());
}
```

## 4. LTV計測の実装

会員登録、チュートリアル突破、課金など任意の成果地点にLTV計測を実装することで、流入元広告のLTVを測定することができます。LTV計測が不要の場合には、本項目の実装を省略できます。

```java:
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(成果地点 ID);
```

LTV計測を行うためには、各成果地点を識別する成果地点IDを指定する必要があります。sendLtvConversionの引数に発行されたIDを指定してください。

課金計測を行う場合には、課金が完了した箇所で以下のように課金額と通貨コードを指定してください。

```java:
import jp.appAdForce.android.LtvManager;
// ...
LtvManager ltv = new LtvManager(ad);
ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
ltv.sendLtvConversion(成果地点ID);
```

LtvManager.URL_PARAM_CURRENCYには[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)で定義された通貨コードを指定してください。

[タグを利用したLTV計測について](./doc/ltv_browser/ja/)

## 5. アクセス解析の実装

自然流入と広告流入のインストール数比較、アプリケーションの起動数やユニークユーザー数(DAU/MAU)、継続率等を計測することができます。アクセス解析が不要の場合には、本項目の実装を省略できます。

アプリケーションの起動、及びバックグラウンドからの復帰を計測するために、各ActivityのonResume()にコードを追加します。

```java:
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {
	@Override
	protected void onResume() {
		super.onResume();
		AdManager ad = new AdManager(this);
		ad.sendReengageConversion(getIntent());

		AnalyticsManager.sendStartSession(this);
	}
}
```

> ※アプリケーションが複数の Activity を生成する場合には、それぞれの onResume()に処理を 追加してください。アプリケーションがバックグラウンドから復帰した際に、その Activity に起 動計測の実装がされていない場合など、正確なアクティブユーザー数が計測できなくなります。

[アクセス解析による課金計測](./doc/analytics_purchase/ja/)


## 6. ProGuardを利用する場合

ProGuard を利用してアプリケーションの難読化を行う際は F.O.X SDK のメソッドが対象とならないよう、以下の設定 を追加してください。

```
-keepattributes *Annotation*

-libraryjars libs/AppAdForce.jar
-keep interface jp.appAdForce.** { *; }
-keep class jp.appAdForce.** { *; }
-keep class jp.co.dimage.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn jp.appAdForce.android.ane.AppAdForceContext
-dontwarn jp.appAdForce.android.ane.AppAdForceExtension
-dontwarn com.adobe.fre.FREContext
-dontwarn com.adobe.fre.FREExtension
-dontwarn com.adobe.fre.FREFunction
-dontwarn com.adobe.fre.FREObject
-dontwarn com.ansca.**
-dontwarn com.naef.jnlua.**
```

また、Google Play Service SDK を導入されている場合は、以下のページで記載されている keep 指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)


## 疎通テストの実施

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


[リエンゲージメント計測を行う場合のテスト手順](./doc/reengagement_test/ja/)


## その他機能の実装

[プッシュ通知の実装](./doc/notify/ja/)

[オプトアウトの実装](./doc/optout/ja/)

## 最後に必ずご確認ください（これまで発生したトラブル集）

### URLスキームの設定がされずリリースされたためブラウザからアプリに遷移ができない

Cookie 計測を行いブラウザを起動した場合には、URL スキームを利用してアプリケーションに遷移します。 この際、独自の URL スキームが設定されている必要があります。


### URLスキームに大文字が含まれ、正常にアプリに遷移されない

環境によって、URLスキームの大文字小文字が判別されないことにより正常に URLスキームの遷移が行えない場合があります。URLスキームは全て小文字で設定を行ってください。


### F.O.Xで確認できるインストール数の値がGoogle Play Developer Consoleの数字より大きい

F.O.Xではいくつかの方式を組み合わせて端末の重複インストール検知を行っています。
重複検知が行えない設定では、同一端末でも再インストールされる度にF.O.Xは新規のインストールと判定してしまいます。

重複検知の精度を向上するために、以下の設定を行ってください。

[広告IDを利用するためのGoogle Play Services SDKの導入](./doc/google_play_services/ja/)

[（オプション）外部ストレージを利用した重複排除設定](./doc/external_storage/ja/)
