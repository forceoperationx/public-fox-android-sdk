## 讓多個INSTALL_REFERRER Receiver共存的設定

只能定義一個與"com.android.vending.INSTALL_REFERRER"相對應的Receiver Class。

如果F.O.X以外的SDK已經定義了與"com.android.vending.INSTALL_REFERRER"相對應的Receiver Class，可以利用從F.O.X SDK裡提供的Receiver Class調用其他Receiver Class的方式實現共存。

### 讓2個INSTALL_REFERRER Receiver共存

請編輯AndroidManifest.xml，追加下面的設定。

```xml
<!-- Receiver Class指定F.O.X SDK的Class -->
<receiver
	android:name="jp.appAdForce.android.InstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- 從F.O.X SDK調用其他Receiver Class的資訊作為meta-data來記述 -->
<meta-data
		android:name="APPADFORCE_FORWARD_RECEIVER"
		android:value="com.example.InstallReceiver" />
```

### 讓3個或以上INSTALL_REFERRER的Receiver共存

當定義3個或以上Receiver的時候，以一個Receiver定義為主軸，在這個Class裡順序調用其他各個Receiver。

下面記述了安裝實例。

* MyReceiver Class（可以按喜好變更Class名）

```java
public class MyReceiver extends BroadcastReceiver {
	// F.O.X的INSTALL_REFERRER Receiver
   jp.appAdForce.android.InstallReceiver foxReceiver;

   // F.O.X以外的INSTALL_REFERRER Receiver
   jp.co.android.sample.InstallReceiver thirdPartyReceiver;
   jp.co.android.sample.InstallReceiver2 thirdPartyReceiver2;

   public MyReceiver() {
      foxReceiver = new jp.appAdForce.android.InstallReceiver();
      thirdPartyReceiver = new jp.co.android.sample.InstallReceiver();
      thirdPartyReceiver2 = jp.co.android.sample.InstallReceiver2();
   }
   @Override
   public void onReceive(Context context, Intent intent){
       foxReceiver.onReceive(context, intent);

       thirdPartyReceiver.onReceive(context, intent);

       thirdPartyReceiver2.onReceive(context, intent);
   }
}
```

* AndroidManifest.xml

```xml
<receiver
       android:name="jp.co.sample.android.receiver.MyReceiver"
       android:exported="true" >
       <intent-filter>
            <action android:name="com.android.vending.INSTALL_REFERRER" />
       </intent-filter>
</receiver>
```

---
[TOP](/lang/zh-tw/README.md)
