## Setting of deduplication using external storages

By preserving identification ID, that SDK created when first launching of application, into local storage or, SD card, it is able to conduct deduplication when reinstalling application.

This setting is optional, however, it helps improving the accuracy of duplicating detection when reinstalling application, so we recommend the implementation.

### Setting of permission

Add the following permissions for reading and writing file to external storage into &lt;manifest&gt; tag of AndroidManifest.xml.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

In the case of setting the above permission, identification ID file is preserved into below path.

```
{Environment.getExternalStorageDirectory().getPath()}/{Application's package name}/__FOX_XUNIQ__
```

### （Optional）Change of preserved directory and file name

The directory name of the file to be saved is the same as package name by default, however, you can change it to an arbitrary directory name and file name by adding the following setting in the &lt;application&gt; tag.

```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="Arbitrary directory name" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="Arbitrary file name" />
```

> Even in the case of specifying the arbitrary directory name and filename, it is created under the path of the return value of Environment.getExternalStorageDirectory().getPath(). The return value of Environment.getExternalStorageDirectory().getPath() changes depending on the terminal, ot OS version.

> In the case of specifying the arbitrary file name without specifying APPADFORCE_ID_DIR(arbitrary directory name), directory with package name will be created and preserved with arbitrary file name under it.

> ※ In the case of specifying directory name without specifying APPADFORCE_ID_FILE(arbitrary file name), directory with arbitrary name will be created, and directory with the name FOX_XUNIQ will be preserved under it.
Normally, this setting is not necessary.

### Example of setting

Example of setting AndroidManifest.xml is noted next.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

In the case of the above example, file path will be next name.

```
{Environment.getExternalStorageDirectory().getPath()}/fox_id_dir/fox_id_file
```

### Suspension of external storage

```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

By this setting, the record towards external storages will be stopped, however, accurate installation measurement will not be conducted because data is always initialized by deleting application.

### Caution for Android M(6.0)

If your app use the function requiring permission that protectionLevel is specified as dangerous, it must ask the user to grant the permission. In the case of not granted by users, the setting of deduplication is not available because of being able to preserve data into storage. READ_EXTERNAL_STORAGE and WRITE_EXTERNAL_STORAGE are specified as dangerous, so the implementation is necessary to request the permissions.

* [Implementation reference](https://developer.android.com/training/permissions/requesting.html#perm-request)

[Example of the implementation]
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
[TOP](/lang/en/README.md)
