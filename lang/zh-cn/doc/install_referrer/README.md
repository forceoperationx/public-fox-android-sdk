[TOP](../../README.md)　>　使多个INSTALL_REFERRER RECEIVER共存的设置

---

## 使多个INSTALL_REFERRER RECEIVER共存的设置

"com.android.vending.INSTALL_REFERRER"对应的Receiver类只能定义一个。

F.O.X之外的SDK等，已经定义"com.android.vending.INSTALL_REFERRER"对应的Receiver类时，可以使用F.O.X SDK的Receiver类，调出其他Receiver类来实现共存。

请编辑AndroidManifest.xml，添加以下的设置。

```xml
<!-- Receiver类指定F.O.X SDK的类 -->
<receiver
	android:name="co.cyberz.fox.FoxInstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- 希望用F.O.X SDK呼出其他Receiver类时信息记录为meta-data -->
<meta-data
		android:name="APPADFORCE_FORWARD_RECEIVER"
		android:value="com.example.InstallReceiver1|com.example.InstallReceiver2|com.example.InstallReceiver3" />
```

> 指定到`APPADFORCE_FORWARD_RECEIVER`的类请附上Package。另外，可以用`|`(竖线)隔开多个类。

> 使用Proguard时，APPADFORCE_FORWARD_RECEIVER中的类指定为-keep且不要更改类名称。<br>
请注意，如果成为Proguard的对象，F.O.X SDK将无法正常搜索类。

---
[Top](../../README.md)
