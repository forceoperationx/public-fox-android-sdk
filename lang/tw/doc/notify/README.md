## PUSH通知的安裝

F.O.X的PUSH通知是利用Google提供的GCM(Google Cloud Messaging for Android)來向用戶的移動終端做PUSH通知的功能。
為了進行PUSH通知，請進行如下安裝。

## 追加gcm.jar

- 拷貝gcm.jar到Android項目的「libs」目錄下。
- 選擇Android項目，點擊右鍵→選擇「Property」。
- 從左側菜單裡選擇「Java Build Path」。
- 從右側主畫面裡選擇「Libraries」標籤。
- 選擇主畫面右側的「Add JARs」。
- 選擇事先拷貝到Android項目「libs」目錄下的「gcm.jar」。
- 確認「gcm.jar」是否已被追加到鏈接類庫。

![notify01](./img01.png)
![notify02](./img02.png)

## AndroidManifest.xml的設定

### permission的設定

為了接收PUSH通知，請按下面那樣把必要的Permission設定追加到\<manifest\>tag裡。

```xml
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="APP的Package名.permission.C2D_MESSAGE" />
<permission android:name="APP的Package名.permission.C2D_MESSAGE" android:protectionLevel="signature" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### PUSH通知用的Receiver的設定

為了接收PUSH通知，請按下面那樣把必要的Receiver設定追加到\<application\>tag裡。

```xml
<receiver android:name="jp.appAdForce.android.NotifyReceiver"
	android:permission="com.google.android.c2dm.permission.SEND">
	<intent-filter>
		<action android:name="com.google.android.c2dm.intent.RECEIVE" />
		<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
		<category android:name="APP的Package名" />
	</intent-filter>
</receiver>
```

### 讓2個receiver共存

只能在com.google.android.c2dm.intent.RECEIVE和com.google.android.c2dm.intent.REGISTRATION相對應的Receiver Class裡選擇一個。如果App需要兩個receiver Class，請追加下面的設定。

```xml
<meta-data android:name="APPADFORCE_NOTIFY_RECEIVER" android:value="想要共存的F.O.X以外的receiver Class" />
```

在內部從jp.appAdForce.android.NotifyReceiver類，調用想要共存的receiver Class的onResume()、或onMessage()、onRegistered()。


## Main Activity的設定

在APP啟動時調用的Activity的onCreate()方法裡安裝下面的處理。

```java
import jp.appAdForce.android.AdManager;
import jp.appAdForce.android.NotifyManager;

@Override
public void onCreate(Bundle savedInstanceState) {

	//...

	AdManager ad = new AdManager(this);
	ad.sendConversion("default");

	NotifyManager notifyManager = new NotifyManager(this, ad);
	String regId = notifyManager.getRegistrationId();
	if ("".equals(regId)||regId == null) {
		GCMRegistrar.register(this, ××××);
	}
}
```

在GCMegistrar.register的第二個參數裡輸入Project編號。

### 有關Project編號とAPI KEY的取得

* 訪問https://console.developers.google.com/project。
* 如果沒有建立API項目，請按下「Create Project」新建一個項目。

![notify03](./img03.png)

* 選擇項目。
* 畫面上方會顯示項目編號，請查看那個值。


![notify04](./img04.png)


* 從畫面左側菜單裡，選擇「API和認證」＞「API」。
* 獎Google Cloud Messaging for Android的狀態設為ON。

![notify06](./img05.png)

* 從畫面左側菜單裡，選擇「API和認證」＞「認證信息」。

![notify06](./img06.png)

* 如果沒有建立API Key、請按下訪問公開API的「建立新Key」、選擇「Server Key」、新建API Key。


* 作成したキーのAPIキーを取得します。
* 取得新建成的Key的API Key。


![notify06](./img08.png)


## 指定遷移目的地

點擊PUSH通知內容的時候，可以打開APP裡的任意地點。可以在FOX的管理畫面裡設定遷移目的地的URL Scheme。

請使用AndroidManifest.xml，在點PUSH通知希望啟動的Activity標籤裡設定URL Scheme。

例如，使用"foo://bar"這樣的URL Scheme，記述了希望啟動ExampleActiviy的設定


```xml
<activity android:name=".ExampleActivity" >
	<intent-filter>
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="android.intent.category.BROWSABLE" />
		<action android:name="android.intent.action.VIEW" />
		<data android:host="bar" android:scheme="foo" />
	</intent-filter>
</activity>
```


---
[TOP](/lang/tw/README.md)
