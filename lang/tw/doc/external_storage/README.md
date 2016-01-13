## 利用外部存儲設定重複排除（Option）

將APP初次啟動時，SDK生成的識別ID會保存在本地存儲和外部存儲（SD card）裡，在APP再安裝時能做重複判定。雖然本設定並非必須，但為了提高監測再安裝APP時的重複精度，建議設定會較好。

### permission的設定

在<manifest>tag裡追加對外部存儲的文件讀寫所需要的Permission設定。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

如果設定了上述Permission，識別ID文件將被保存在下面的路徑。

```
能夠用Environment.getExternalStorageDirectory().getPath()取得路徑/APP的Package名/__FOX_XUNIQ__
```

### （任意）變更保存目錄和文件名

默認使用Package名作為用於保存文件的目錄名，也可以改變為任意的目錄名和文件名。當然也可以選擇不保存文件。
如果需要控制識別ID保存的路徑，需要在<application>tag裏添加如下的設定。


```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="任意的目錄名" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="任意的文件名" />
```

> 指定的任意的目錄名和文件名，會在Environment.getExternalStorageDirectory().getPath()返回路徑值的下面生成。在不同的設備或系統版本中Envrionment.getExternalStorageDirectory().getPath()的返回值不總是一樣的。<br>

> 沒有指定”APPADFORCE_ID_DIR”（任意的目錄名），只指定了文件名的話，會自動創建APP的Package名的目錄，在目錄下按指定名文件保存。<br>

> 沒有指定”APPADFORCE_ID_FILE”（任意的文件名），只指定了目錄名的話，在指定的目錄下會創建以”__XUNIQ__”為名的文件。通常不需要設定。


### 設定例

下面記述了AndroidManifest.xml設定實例。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

在上面的例子裡，保存文件的路徑如下：

```
用Environment.getExternalStorageDirectory().getPath()能夠取得的路徑是/fox_id_dir/fox_id_file
```

### 停止使用外部存儲

希望讓Force Operation X SDK停止訪問外部存儲的時候，請在AndroidManifest.xml裡追加APPADFORCE_USE_EXTERNAL_STORAGE的設定。
```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

通過這個設定可以停止紀錄到外部存儲，但因為刪除APP會初始化數據，將無法進行正確的Install計測。


### Android M(6.0)的注意事項

為了利用ProtectionLevel設定為`dangerous`權限的機能，需要通過用戶許可。如果用戶不許可，數據無法保存到存儲領域，進而無法利用重複排除設定。
前面說到的READ_EXTERNAL_STORAGE和WRITE_EXTERNAL_STORAGE的級別也屬於dangerous，需要做安裝來獲得用戶許可。


* [安裝參考](https://developer.android.com/training/permissions/requesting.html#perm-request)

[安裝實例]
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

    // 在M移動終端執行時，Install計測前用checkSelfPermission檢查permissions
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
      if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
              && checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
        // 已經取得了permission的時候
        sendFoxConversion();
      } else {
        // 向用戶要求permission的處理
        requestPermissions(
                new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
                PERMISSION_REQUEST_CODE_FOX);
      }
    } else {
      // M之外的移動終端在Install的時候取得了permission，可以直接執行Install計測
      sendFoxConversion();
    }
  }

	/**
	 * F.O.X SDK的Install計測處理方法
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
          // 得到用戶的認可，執行Install計測處理
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
[TOP](/lang/tw/README.md)
