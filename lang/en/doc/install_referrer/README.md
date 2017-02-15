## Setting in the case of making multiple INSTALL_REFERRER receivers coexist

It define only one receiver class to "com.android.vending.INSTALL_REFERRER".

In the case that the receiver class is already defined to "com.android.vending.INSTALL_REFERRER" such as SDK excluding F.O.X, it is able to make them coexist by calling other receiver classes from receiver classes F.O.X SDK provides.

### In the case of making two INSTALL_REFERRER receivers coexist

Edit AndroidManifest.xml and please add the following setting.

```xml
<!-- Receiver class specifies the class of F.O.X SDK -->j
<receiver
	android:name="jp.appAdForce.android.InstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- The information of receiver class calling from F.O.X SDK is noted as meta-data -->
<meta-data
		android:name="APPADFORCE_FORWARD_RECEIVER"
		android:value="com.example.InstallReceiver" />
```

> When using ProGuard, make sure that the class name specified by `APPADFORCE_FORWARD_RECEIVER` is not changed by -keep specification.<br>
Please be aware that F.O.X SDK will not be able to find classes and will not work properly if the class is subject to obfuscated by ProGuard.


### In the case of making more than three INSTALL_REFERRER receivers coexist

When incorporating more than three receivers, define the receiver which can be the axis and set to call each receiver in-line in the class.

Example of the implementation is noted at the following.

* MyReceiver Class (You can change class name as you want.)

```java
public class MyReceiver extends BroadcastReceiver {
	// F.O.X's INSTALL_REFERRER receiver
	jp.appAdForce.android.InstallReceiver foxReceiver;

	//  INSTALL_REFERRER receiver exclusing F.O.X
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
[TOP](/lang/en/README.md)
