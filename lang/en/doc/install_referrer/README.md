[TOP](../../README.md)　>　Enabling multiple INSTALL_REFERRER receivers

---

## Enabling multiple INSTALL_REFERRER receivers

Only one receiver class that catches the Google Play Store `install receiver` can be defined.

If your application already contains another broadcast receiver, please add the following code to enable
the F.O.X SDK to call your receiver class(es). This way, multiple receiver classes can be defined.

Edit your `AndroidManifest.xml` as follows.

```xml
<!-- set the receiver class to the F.O.X SDK's install receiver -->
<receiver
	android:name="co.cyberz.fox.FoxInstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- Add any install receivers you want F.O.X to call to your meta-data -->
<meta-data
		android:name="APPADFORCE_FORWARD_RECEIVER"
		android:value="com.example.InstallReceiver1|com.example.InstallReceiver2|com.example.InstallReceiver3" />
```
> include the package name when you set install receivers in your `APPADFORCE_FORWARD_RECEIVER` meta-data. You can add multiple receivers with the `|`(pipe) symbol.

> If your app uses Proguard, use `-keep` to prevent the class name from changing in the `APPADFORCE_FORWARD_RECEIVER`. <br>
If the F.O.X SDK is targeted by Proguard, then it will not be able to search for the class, and will not function correctly.

---
[Return to Top](../../README.md)
