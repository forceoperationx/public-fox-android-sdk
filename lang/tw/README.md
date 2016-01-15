# Force Operation X是什麼

Force Operation X (下面簡稱F.O.X)是基於智慧手機的，用來最大改善廣告效果的綜合解決方案平台。除了對APP下載量和網絡用戶操作的基本計測外，還能基於手機用戶行为特性採用獨自效果計測基準，實現了企業宣傳推廣时費用与效果比的最大改善。

在這個文檔裡，詳細講解了基於智慧手機平台優化廣告效果的F.O.X SDK的導入步驟。

## F.O.X SDK是什麼

在APP裡導入F.O.X SDK的方法來實現如下功能。

* **Install計測**

能够按不同的广告流入来計測安装数。

* **LTV計測**

按不同的廣告流入来計測Life Time Value。作為主要的成果地點，有會員登錄，教程突破，消费等。能夠按照不同廣告来監測登錄率，消費率和消費額等。

* **流量分析**

自然流入和廣告流入的APP安裝數比較。能夠計測APP的啟動數，唯一用戶數(DAU/MAU)，持續率等。

* **PUSH通知**

使用F.O.X監測的信息，能夠針對用戶來做PUSH通知。比如，能夠針對特定廣告流入用戶來發送消息。

## 1. 安裝

請從下面的頁面來下載最新的SDK。

* [SDK發布頁面](https://github.com/cyber-z/public_fox_android_sdk/releases)

已經在APP裡導入了SDK的話，請參考[有關更新到最新版本](./doc/update/README.md)。

請展開下載的SDK「FOX_Android_SDK_<version>.zip」，把「AppAdForce.jar」導入到APP的項目裡。


* [Eclipse項目的導入方法](./doc/integration/eclipse/README.md)
* [AndroidStudio項目的導入方法](./doc/integration/android_studio/README.md)

## 2. 設定

* **SDK設定**

為使SDK發揮作用請在AndroidManifest.xml裡追加必要的設定。

### permission的設定

F.O.X SDK利用下面4種permission。
請在&lt;Manifest&gt;tag內追加如下permission設定。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

permission|Protection Level|必須|概要
:---|:---:|:---:|:---
INTERNET|Normal|必須|F.O.X SDK進行通信時必須。
ACCESS_NETWORK_STATE|Normal|必須|F.O.X SDK確認可否通信時必須。
READ_EXTERNAL_STORAGE ※1|Dangerous|任意|利用外部存儲提高重複排重功能時必須。(※2)
WRITE_EXTERNAL_STORAGE ※1|Dangerous|任意|利用外部存儲提高重複排重功能時必須。(※2)

> ※1 READ_EXTERNAL_STORAGE和WRITE_EXTERNAL_STORAGE權限是利用把數據保存在外部存儲器，為了再安裝APP的時候更準確地進行Install計測而必須設定的權限，屬於任意設定。

> ※2 Android M為了利用ProtectionLevel設定為`dangerous`權限的機能，需要獲得用戶許可。詳細請查看[利用外部存儲設定重複排除](/lang/tw/doc/external_storage/README.md)

### meta-data的設定

為了執行SDK，請將必要的訊息追加到<application>tag內。

```xml
<meta-data
	android:name="APPADFORCE_APP_ID"
	android:value="請輸入Force Operation X管理員告知的值。" />
<meta-data
	android:name="APPADFORCE_SERVER_URL"
	android:value="請輸入Force Operation X管理員告知的值。" />
<meta-data
	android:name="APPADFORCE_CRYPTO_SALT"
	android:value="請輸入Force Operation X管理員告知的值。" />
<meta-data
	android:name="ANALYTICS_APP_KEY"
	android:value="請輸入Force Operation X管理員告知的值。" />
```

審定的Key和Value如下：

|參數名|必須|概要|
|:------|:------|:------|
|APPADFORCE_APP_ID|必須|請輸入Force Operation X管理員告知的值。|
|APPADFORCE_SERVER_URL|必須|請輸入Force Operation X管理員告知的值。|
|APPADFORCE_CRYPTO_SALT|必須|請輸入Force Operation X管理員告知的值。|
|ANALYTICS_APP_KEY|必須|請輸入Force Operation X管理員告知的值。|


### Install referrer計測的設定
將Install referrer計測所需要的設定追加在<application>tag內。

```xml
<receiver android:name="jp.appAdForce.android.InstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

如果"com.android.vending.INSTALL_REFERRER"的receiver class已經被定義，請參照[讓多個INSTALL_REFERRER R seceiver共存的設定](./doc/install_referrer/README.md)

### URL scheme設定

為了能夠從外部啟動APP。請在要啟動的對象的&lt;activity&gt;tag內追加如下設定。

```xml
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
	<data android:scheme="輸入任意的URL Scheme" />
</intent-filter>
```

> 由於環境的不同，可能無法判別URL Scheme裡的大小寫字母，進而引起不能正常跳轉。因此URL Scheme請全部使用小寫字母來設定。

### 導入Google Play Services SDK來使用廣告ID

為了能使用廣告ID，必須導入Google Play Services SDK。即使Google Play Services SDK沒被導入，F.O.X SDK也能起作用，但若不利用廣告ID會導致無法計測一部分廣告項目，或降低重複判定的精度。導入詳細步驟請參考[導入Google Play Services SDK來使用廣告ID](./doc/google_play_services/README.md)。

### AndroidManifest.xml實例

[AndroidManifest.xml實例](./doc/config_android_manifest/AndroidManifest.xml)

## 3. Install計測的安裝

安裝了初次啟動時的Install計測處理，就能夠測定廣告效果了。請編輯項目的源代碼，按下面的那樣來安裝。

請在啟動APP時調用的Activity的onCreate()裡面安裝sendConversion方法。

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);

	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}
```

通常按上面那樣指定"default"到sendConversion的參數裡

[sendConversion的詳細](./doc/send_conversion/README.md)

為了進行Reengagement廣告的計測（經由URL Scheme啟動的計測），請在全部設定了URL Scheme的`Activity(※1)`的onResume()裡安裝`sendReengageConversion`方法。

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendReengageConversion(getIntent());
}
```

如果使用URL Scheme啟動Activity的launchMode設定為"singleTask"或"singleInstance"，為了通過URL Scheme來接收參數，請重寫onNewIntent方法，並按下面那樣調用`setIntent`方法。
```java
@Override
protected void onNewIntent(Intent intent)
{
	super.onNewIntent(intent);
	setIntent(intent);
}
```

> ※1 為了計測Reengagement廣告，需要在AndroidManifest.xml定義的Acitvity裡定義定制化的URL Scheme。本次計測是利用定制URL Scheme調用Activity的方法來進行Reengagement計測的。

## 4. LTV計測的安裝

通過在會員登錄，教程突破，消費等任意的成果地點安裝LTV計測，能夠測定不同廣告流入的LTV。如果不做LTV計測，可以省略本項目的安裝。

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(成果地点ID);
```

為了進行LTV計測，必須指定識別各成果地點的成果地點ID。請在`sendLtvConversion`方法的參數裡指定發行的ID。

進行消費計測的時候，請按照下面那樣在完成消費處理的地方指定消費額和貨幣代碼。


```java
import jp.appAdForce.android.LtvManager;
// ...
LtvManager ltv = new LtvManager(ad);
ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
ltv.sendLtvConversion(成果地点ID);
```

在LtvManager.URL_PARAM_CURRENCY裡請按[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)定義的貨幣代碼來指定。

[有關利用Tag的LTV計測](./doc/ltv_browser/README.md)

## 5. 流量分析的安裝

自然流入和廣告流入的安裝數比較。能夠計測APP的啟動數，唯一用戶數(DAU/MAU)，持續率等。如果不做流量分析，可以省略本項目的安裝。

為了計測APP的啟動和計測從後台到前台的恢復，請在各Activity的onResume()裡追加`AnalyticsManager.sendStartSession`方法。

```java
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

> ※APP在產生複數的Activity時，請將處理追加到所有Activity的onResume()。APP從Background恢復到活動狀態時，若尚未導入啟動計測到Activity，將無法計測正確的活躍用戶數。

[依靠流量分析進行Event計測](./doc/analytics_event/README.md)

[依靠流量分析進行消費計測](./doc/analytics_purchase/README.md)


## 6. 使用ProGuard

使用ProGuard進行APP讀取混淆化時，請進行以下設定，將F.O.X SDK的method排除在對象外。

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
-dontwarn com.adobe.fre.FREContext
-dontwarn com.adobe.fre.FREExtension
-dontwarn com.adobe.fre.FREFunction
-dontwarn com.adobe.fre.FREObject
-dontwarn com.ansca.**
-dontwarn com.naef.jnlua.**
```

如果已導入GooglePlayServiceSDK，請確認下面網頁所記載的keep指定是否有被記述。

[導入Google Play Services時的Proguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)


## 7. 進行疏通測試

在APP上架申請以前，在導入SDK的狀態請做充分的測試，以確保APP的動作沒有問題。

由於在啟動後只發生一次Install計測的通信，如果想要再次進行Install計測的話，請卸載APP再次安裝

**測試步驟**

1. 如果測試用的設備已安裝APP，請先卸載掉APP
1. 刪除測試終端設備的默認瀏覽器的Cookie
1. 點選由弊司發行的測試用URL
1. 畫面移轉到Market
1. 在測試用的移動終端上安裝測試APP<br />
1. 啟動APP，瀏覽器啟動<br />
若流覽器無法啟動，說明沒有正常設定。請重新設定，若仍無法發現問題，請與弊司聯繫。
1. 把畫面移轉到LTV地點<br />
1. 結束並從後台關閉APP<br />
1. 再次啟動APP<br />

請告訴鄙司3，6，7，9的時間。在鄙司這邊會確認是否正常被計測。待確認沒有問題的時候，測試算正式完成。

> 測試用的URL請務必在默認瀏覽器裡使用。郵件APP，QR碼讀取APP等這些APP內部會用WebView發生跳轉，因此無法進行計測。

> 點擊測試URL的時候，有時候因沒有跳轉目的地而彈出錯誤對話框，這個不影響疏通測試。

[Reengagement計測時的疏通測試](./doc/reengagement_test/README.md)


## 8. 其他機能的安裝

* [PUSH通知的安裝](./doc/notify/README.md)

* [Opt-Out的安裝](./doc/optout/README.md)


## 9. 最後請務必確認（到現在發生過的問題集）

### 9.1. 未設定URL Scheme發布的APP引起無法從瀏覽器跳轉到APP

為了進行Cookie計測，在啟動外部瀏覽器以後，要利用URL Scheme跳轉到APP來返回到原來的畫面。這時有必要設定獨自的URL Scheme，未設定URL Scheme發布的APP將無法正常跳轉。


### 9.2. URL Scheme裡包含了大寫字母，無法正常跳轉回APP

由於環境的不同，可能無法判別URL Scheme裡的大小寫字母，進而引起不能正常跳轉。因此URL Scheme請全部使用小寫字母來設定。


### 9.3. 用F.O.X計測的Install數值比Google Play Developer Console的數值要大

F.O.X使用了多種方式來監測移動終端的重複安裝。
倘若設定了不進行重複監測，在相同移動終端再安裝時F.O.X會判定為新的安裝。

為了提高重複監測的精度，請進行如下設定。

* [導入Google Play Services SDK來使用廣告ID](./doc/google_play_services/README.md)。

* [利用外部存儲設定重複排除](./doc/external_storage/README.md)

* [Android M(6.0) 利用自動備份功能](./doc/auto_backup/README.md)

---
[主菜單](/README.md)
