## 外部ストレージを利用した重複判定設定

アプリケーションの初回起動時にSDKが生成した識別IDをローカルストレージまたはSDカードに保存することで、アプリケーション再インストール時に重複判定を行うことができます。

本設定は必須ではありませんが、アプリケーションの再インストールにおける重複検知の精度が大きく向上するため、実装を推奨しております。

### パーミッションの設定

外部ストレージへのファイル読み書きに必要なパーミッションの設定をAndroidManifest.xmlの<manifest>タグ内に追加します。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

上記パーミッションが設定されている場合、次のパスに識別IDファイルが保存されます。

```
Environment.getExternalStorageDirectory().getPath()で取得できるパス/アプリのパッケージ名/__FOX_XUNIQ__
```

### （任意）保存ディレクトリ及びファイル名の変更

保存されるファイルのディレクトリ名は、標準ではパッケージ名で作成されますが、<application>タグ内に以下設定を追加することで、任意のディレクトリ名及びファイル名に変更することができます。

```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="任意のディレクトリ名" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="任意のファイル名" />
```

> 任意のディレクトリ名やファイル名を指定した場合でも、Environment.getExternalStorageDirectory().getPath()の返り値のパス配下に作成します。Environment.getExternalStorageDirectory().getPath()の返り値は端末やOSバージョンによって異なります。<br>
> "APPADFORCE_ID_DIR"(任意のディレクトリ名)を指定せず、任意のファイル名のみを指定した場合、アプリのパッケージ名のディレクトリが作成され、その配下に任意のファイル名で保存されます。<br>
> ※"APPADFORCE_ID_FILE"(任意のファイル名)を指定せず、任意のディレクトリ名のみを指定した場合、任意の名前でディレクトリが作成され、その配下に"__FOX_XUNIQ__"で保存されます。<br>
通常は設定の必要はありません。


### 設定例

AndroidManifest.xmlの設定例を次に記載します。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

上記の例の場合に、保存されるファイルのパスは次になります。

```
Environment.getExternalStorageDirectory().getPath()で取得できるパス/fox_id_dir/fox_id_file
```

### 外部ストレージの利用停止

Force Operation X SDKによる外部ストレージへのアクセスを停止したい場合には、AndroidManifest.xmlにAPPADFORCE_USE_EXTERNAL_STORAGEの設定を追加してください。
```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

本設定を行うことで外部ストレージに対する記録が停止しますが、アプリケーションの削除によりデータが常に初期化されるため、正確なインストール計測が行われなくなります。


### Android M(6.0)における注意点

protectionLevelがdangerousに指定されているパーミッションを必要とする機能を利用するには、ユーザーの許可が必要となります。
ユーザーの許可がない場合、ストレージ領域へのデータ保存が行えなくなるため重複判定設定が利用出来なくなります。
前述の`READ_EXTERNAL_STORAGE`と`WRITE_EXTERNAL_STORAGE`においてもdangerousとなっており、ユーザーに許可を貰うための実装を行う必要があります。

* [実装の参考](https://developer.android.com/training/permissions/requesting.html#perm-request)

[実装例]
```java
package cyberz.jp.co.test;

import android.Manifest;
import android.annotation.TargetApi;
import android.content.pm.PackageManager;
import android.os.Build;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import jp.appAdForce.android.AdManager;

public class AndroidMMainActivity extends AppCompatActivity {

  private final int PERMISSION_REQUEST_CODE_FOX = 1;

  @TargetApi(Build.VERSION_CODES.M)
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // Mの端末で実行する時に、インストール計測前にcheckSelfPermissionでpermissionsをチェックする
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
      if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
              && checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
        // 既にpermissionを取得出来ている場合
        sendFoxConversion();
      } else {
        // ユーザーにpermissionを要求する処理
        requestPermissions(
                new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
                PERMISSION_REQUEST_CODE_FOX);
      }
    } else {
      // M以外の端末はinstall時にpermissionを取得出来ているため、直ちにinstall計測処理を実行
      sendFoxConversion();
    }
  }

	/**
	 * F.O.X SDKによるインストール計測処理実行メソッド
	 */
  private void sendFoxConversion() {
    AdManager adManager = new AdManager(AndroidMMainActivity.this);
    adManager.sendConversion();
  }

  @Override
  public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    switch (requestCode) {
      case PERMISSION_REQUEST_CODE_FOX:
        if (grantResults.length > 1
                && grantResults[0] == PackageManager.PERMISSION_GRANTED
                && grantResults[1] == PackageManager.PERMISSION_GRANTED) {
          // ユーザーの承認を得てinstall計測処理を実行
          sendFoxConversion();
        }
        break;
      default:
        break;
    }
  }
}
```

---
[トップ](/lang/ja/README.md)
