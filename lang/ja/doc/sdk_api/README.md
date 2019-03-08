# Fox SDK API

<div id="foxconfig"></div>

# FoxConfig

public class FoxConfig

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxConfig

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxConfig** (Context c, int appId, String appKey, String appSalt)<br><br>`c` : Context (ApplicationContext可)<br>`appId` : 管理画面で発行されたアプリID<br>`appKey` : 管理画面で発行されたアプリKEY<br>`appSalt` : 管理画面で発行されたソルト|FOX SDKを動作させるための必須パラメータをセット|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|void|**activate** ( )|設定した必須情報を内部で保存する|
|void|**addOfflineModeOption** (boolean isOffline )<br><br>`isOffline` : オフラインモードの有効無効|F.O.X SDKの計測機能を停止しトラッキングを無効化する<br>`デフォルト : false`|
|FoxConfig|**addDebugOption** (boolean isDebug)<br><br>`isDebug` : デバッグログの出力有無|エラー時のメッセージやスタックトレースをLogcatに出力させる<br>`デフォルト : false`|
|FoxConfig|**addServerUrlOption** (String url)<br><br>`url` : DeliverサーバーURL暗号化文字列|デバッグ用、またはHTTPS/HTTPを切り替える用のURLのセッター<br>※管理者から指定されない限り使用することはありません。|
|FoxConfig|**addAServerUrlOption** (String url)<br><br>`url` : アクセス解析サーバーURL暗号化文字列|デバッグ用、またはHTTPS/HTTPを切り替える用のURLのセッター<br>※管理者から指定されない限り使用することはありません。|
|FoxConfig|**addDServerUrlOption** (String url)<br><br>`url` : DahliaサーバーURL暗号化文字列|デバッグ用、またはHTTPS/HTTPを切り替える用のURLのセッター<br>※管理者から指定されない限り使用することはありません。|

<div id="annotation_foxconfig"></div>

# FoxConfig

public interface FoxConfig

Added in 4.3.0

必須情報をアノテーションで定義するインターフェース

### Public methods

|Type|Field|Details|
|:---:|:---|:---|
|int|appId|アプリIDの指定|
|String|appKey|AnalyticsAppKeyの指定|
|String|appSalt|AppSaltの指定|
|String|serverUrl|サーバーURLの指定(デバッグ用)|
|String|aServerUrl|アクセス解析用サーバーURLの指定(デバッグ用)|
|boolean|isDebug|デバッグモードの有効化<br>デフォルトはfalse|
|boolean|isAutoInstallTracking|初回起動計測の自動化の設定<br>デフォルトはtrue|
|boolean|isAutoDeeplinkTracking|ディープリンク経由での計測の自動化の設定<br>デフォルトはtrue|
|boolean|isAutoSessionTracking|アクセス解析のセッション計測の自動化の設定<br>デフォルトはtrue|

<div id="fox"></div>

# Fox

public enum Fox<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;	java.lang.Enum&lt;E extends [java.lang.Enum](https://developer.android.com/reference/java/lang/Enum.html)&lt;E&gt;&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.Fox

### Public instance

* **`AUTOMATOR`** : 自動計測機能を実装する場合に使用 &nbsp;(Added in 4.3.0)

### Public methods

|**Return**|**Type**|**Method**|**Details**|
|:---:|:---:|:---|:---|
|void|static|**trackInstall** ( )|インストール後の初回起動計測用メソッド。管理画面で設定した通りに動作する。|
|void|static|**trackInstall** ([FoxTrackOption](#foxtrackoption) option)<br><br>`option` : 計測に指定するオプション|インストール後の初回起動計測用メソッド。FoxOptionに設定した計測オプションに従い動作する。|
|void|static|**trackSession** ( )|onResumeに実装しセッション計測を行う。|
|void|static|**trackEvent** ([FoxEvent](#foxevent) event)<br><br>`event` : イベント計測に必要な各種パラメータの設定クラス|アプリ内のイベント計測を実行|
|void|static|**trackEventByBrowser** (String url)<br><br>`url` : イベントタグを埋め込んだ外部のWebページのURL|イベントタグを設置した外部のWebページのイベント計測|
|void|static|**trackEventByWebView** (WebView webView)<br><br>`webView` : LTVが発生するWebページにアクセスするWebView|WebViewによるタグ計測。Android L以降、WebViewへサードパーティCookieの編集を行う場合には許可が必要なため引数にWebViewを必要としている。|
|boolean|static|**isConversionCompleted** ( )|インストール計測が完了しているかをbooleanで返す|
|void|static|trackDeeplinkLaunch (Intent intent)<br><br>`intent` : カスタムURLスキームで起動した際のIntent|リエンゲージメント計測・初回起動計測の完了検知・ディファードディープリンクの受信のコントロールを行います。|
|void|static|trackDeeplinkLaunch (Intent intent, String buid)<br><br>`intent` : カスタムURLスキームで起動した際のIntent<br>`buid` : 広告主端末ID(ユーザーIDなど)|リエンゲージメント計測・初回起動計測の完了検知・ディファードディープリンクの受信のコントロールを行います。|
|void|static|trackDeeplinkLaunch (Activity activity)<br><br>`activity` : カスタムURLスキームで起動するActivity|リエンゲージメント計測・初回起動計測の完了検知・ディファードディープリンクの受信のコントロールを行います。|
|void|static|trackDeeplinkLaunch (Activity activity, String buid)<br><br>`activity` : カスタムURLスキームで起動するActivity<br>`buid` : 広告主端末ID(ユーザーIDなど)|リエンゲージメント計測・初回起動計測の完了検知・ディファードディープリンクの受信のコントロールを行います。|
|void|static|**setUserInfo** (JSONObject userInfo)<br><br>`userInfo` : ユーザー情報・属性|計測する際のユーザー情報・属性等を設定する|
|JSONObject|static|**getUserInfo** ( )|setUserInfoでユーザー情報をセットしてある場合に、その情報を取得することができる|
|Fox|-|**init** ( Context context )|アノテーションで必須情報を設定した場合に呼び出すイニシャライズメソッド<br>(Added in 4.3.0)|
|Fox|-|**init** ( [FoxConfig](#foxconfig) config )|手動でイニシャライズ処理を実行する。<br>※アノテーションによる必須情報設定との併用不可。<br>(Added in 4.3.0)|
|-|-|**startTrack** ( ) |自動計測を開始するメソッド<br>(Added in 4.3.0)|
|-|-|**startTrack** ( FoxTrackOption option ) |計測オプションを指定し自動計測を開始する。<br>(Added in 4.3.0)|
|Fox|-|**setManualInstallTrackingEnable** ( boolean manualInstallTracking ) <br><br>`manualInstallTracking` : インストール計測の手動実行有無フラグ|個別に手動でインストール計測の実装を行う場合に使用します。デフォルト : false(自動計測有効)<br>(Added in 4.3.0)|
|boolean|-|**isManualInstallTrackingEnable** ( ) |インストール計測の手動実行有無取得<br>(Added in 4.3.0)|
|Fox|-|**setManualDeeplinkTrackingEnable** ( boolean manualDeeplinkTracking ) <br><br>`manualDeeplinkTracking` : ディープリンク経由計測の手動実行有無フラグ|個別に手動でディープリンク経由計測の実装を行う場合に使用します。デフォルト : false(自動計測有効)<br>(Added in 4.3.0)|
|boolean|-|**isManualDeeplinkTrackingEnable** ( ) |ディープリンク経由計測の手動実行有無取得<br>(Added in 4.3.0)|
|Fox|-|**setManualSessionTrackingEnable** ( boolean manualSessionTracking ) <br><br>`manualSessionTracking` : セッション計測の手動実行有無フラグ|個別に手動でセッション計測の実装を行う場合に使用します。デフォルト : false(自動計測有効)<br>(Added in 4.3.0)|
|boolean|-|**isManualSessionTrackingEnable** ( ) |セッション計測の手動実行有無取得<br>(Added in 4.3.0)|

<div id="foxtrackoption"></div>

# FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|init|**FoxTrackOption** ()|コンストラクタ|
|FoxTrackOption|**setRedirectUrl** (String redirectTo)<br><br>`redirectTo` : ブラウザ起動後のリダイレクト先|Cookie計測を実施する場合のブラウザ起動後のリダイレクト先を指定する。URL/URLスキーム|
|FoxTrackOption|**setBuid** (String buid)|Buidのセッター|
|FoxTrackOption|**setOptOut** (boolean optOut)|OptOut有無のセッター<br>※デフォルト : false|
|void|**setTrackingStateListener** (TrackingStateListener listener)|初回起動計測が成功した際のコールバック。onCompleteメソッドをOverrideする|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)<br><br>`handler` : ディファードディープリンクを受信するためのハンドラ|ディファードディープリンクを利用する際にコールします。|
|FoxEvent|**addExtraInfo** (String key, String val)|任意のkey/valueをセット<br><br>`接頭辞が'_'のKeyは登録不可``|
|HashMap<String, String>|**getExtraInfo** ( )|addExtraInfoで追加したkey/valueをHashMapで取得|


<div id="foxevent"></div>

# FoxEvent

public class FoxEvent<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxEvent

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxEvent** (String eventName)|アクセス解析のイベント計測を行う|
|**FoxEvent** (String eventName, int ltvPointId)|LTV計測とアクセス解析のイベント計測を行う|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|FoxEvent|**addExtraInfo** (String key, String val)|任意のkey/valueをセット|
|HashMap<String, String>|**getExtraInfo** ( )|addExtraInfoで追加したkey/valueをHashMapで取得|
|JSONObject|**getEventInfo** ( )|エンゲージメント計測にて`eventInfo`に入れたJSONObjectを返す|

### Public variables

|**Type**|**Variable**|**Details**|
|:---:|:---|:---|
|String|buid|Buidをセット|
|double|price|金額をセット|
|String|currency|通貨をセット|
|String|sku|skuをセット|
|String|value|valueをセット|
|String|label|labelをセット|
|String|orderId|orderIdをセット|
|int|quantity|quantityをセット|
|JSONObject|eventInfo|eventInfoをセット|

<div id="ids"></div>

# Ids

public class Ids

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.extra.Ids

### Public methods

|**Type**|**Return**|**Method**|**Details**|
|:---:|:---:|:---|:---|
|static|String|*static*<br>**get** ( Context context, String key)|keyには取得するID名を指定します。<br>例：`get(getApplicationContext(), "xuniq")`|


---
[トップ](../../README.md)
