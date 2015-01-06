## 複数のINSTALL_REFERRERレシーバーを共存させる場合の設定

"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスは一つしか定義することができません。

F.O.X以外のSDK等、既に"com.android.vending.INSTALL_REFERRER"に対するレシーバークラスが定義されている場合は、F.O.X SDKが用意しているレシーバークラスから、他のレシーバークラスを呼び出すことで共存させることが可能です。

### 二つのINSTALL_REFERRERレシーバーを共存させる場合

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

### 三つ以上のINSTALL_REFERRERレシーバーを共存させる場合

三つ以上のレシーバーを組み込む際は、一つ軸となるレシーバークラスを定義し、そのクラス内で直列に各レシーバーを呼び出すようにします。

下記に実装例を記載します。

* MyReceiverクラス（クラス名は好きに変更して頂いて構いません）

```java
public class MyReceiver extends BroadcastReceiver {
	// F.O.XのINSTALL_REFERRERレシーバー
   jp.appAdForce.android.InstallReceiver foxReceiver;

   // F.O.X以外のINSTALL_REFERRERレシーバー
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