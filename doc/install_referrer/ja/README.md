## 二つのINSTALL_REFERRERレシーバーを共存させる場合の設定

"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスは一つしか定義することができません。

F.O.X以外のSDK等、既に"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスが定義されている場合は、F.O.X SDKが用意しているレシーバークラスから、他のレシーバークラスを呼び出すことで共存させることが可能です。

AndroidManifest.xmlを編集し、下記の設定を追加してください。

```xml
<!-- レシーバークラスはF.O.X SDKのクラスを指定します -->
<receiver
	android:name="jp.appAdForce.android.InstallReceiver"
	android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>

<!-- F.O.X SDKから呼び出したい他のレシーバークラス情報をmeta-dataとして記述します -->
<meta-data
	android:name="APPADFORCE_FORWARD_RECEIVER"
	android:value="com.example.InstallReceiver" />
```

> 三つ以上のレシーバークラスを共存させたい場合は、Force Operation X管理者までお問い合わせください。
