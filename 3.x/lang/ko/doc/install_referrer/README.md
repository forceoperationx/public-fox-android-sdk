## 여러개의 INSTALL_REFERRER 리시버를 공존시키는 경우의 설정

"com.android.vending.INSTALL_REFERRER"에 대한 리시버 클래스는 하나 밖에 정의 할 수 없습니다.

F.O.X 이외의 SDK 등 이미 "com.android.vending.INSTALL_REFERRER"에 대한 리시버 클래스가 정의되어있는 경우, F.O.X SDK가 제공하고 있는 리시버 클래스에서 다른 리시버 클래스를 호출하여 공존시킬 수 가능합니다.

### 두개의 INSTALL_REFERRER 리시버를 공존시키는 경우

AndroidManifest.xml를 편집하고 다음 설정을 추가하십시오.

```xml
<!-- 리시버 클래스는 F.O.X SDK의 클래스를 지정합니다 -->
<receiver
	android:name="jp.appAdForce.android.InstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- F.O.X SDK에서 호출하고 싶은 다른 리시버 클래스 정보를 meta-data로 설명합니다 -->
<meta-data
		android:name="APPADFORCE_FORWARD_RECEIVER"
		android:value="com.example.InstallReceiver" />
```

### 세 개 이상의 INSTALL_REFERRER 리시버를 공존시키는 경우

세 개 이상의 리시버를 통합 할 때는 하나의 축이되는 리시버 클래스를 정의하고 그 클래스에서 직렬로 각 수신기를 호출하도록합니다.

아래에 구현 예를 기재합니다.

* MyReceiver클래스（클래스명은 자유롭게 변경해 주십시오.）

```java
public class MyReceiver extends BroadcastReceiver {
	// F.O.X의 INSTALL_REFERRER 리시법
   jp.appAdForce.android.InstallReceiver foxReceiver;

   // F.O.X 이외의 INSTALL_REFERRER 리시버
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
[TOP으로](/3.x/lang/ko/README.md)
