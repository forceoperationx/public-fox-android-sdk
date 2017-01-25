[TOP](../../README.md)　>　利用外部储存来优化排除重复

---

# 利用外部储存来优化排除重复

APP首次启动时，SDK生成的识别ID保存在本地储存或外部SD卡中，可以在APP再次安装时利用该ID进行重复判定。

本设置为可选项目，但为了提高APP再次安装时重复检查的精度，建议使用。

## Permission的设置

在AndroidManifest.xml的tag中添加读写外部储存必要的权限设置。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

已设置上述权限的情况，ID识别文件会保存在以下路径。

```
用Environment.getExternalStorageDirectory().getPath()可获取路径/APP的Package名/__FOX_XUNIQ__
```

## （任意）修改保存目录及文件名

保存文件的目录名，一般为Package名，在&lt;application&gt;Tag下添加以下设置可以修改目录名以及文件名。

```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="任意目录名" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="任意文件名" />
```

> 指定其他目录名和文件名时，也是在
Environment.getExternalStorageDirectory().getPath()的返回值路径下生成。Environment.getExternalStorageDirectory().getPath()的返回值根据终端和OS版本的不同而有所差异。<br>
> 未指定"APPADFORCE_ID_DIR"(任意目录名)，仅指定文件名时，会生成APP的Package名的目录，并在该路径下保存文件。<br>
> ※未指定"APPADFORCE_ID_FILE"(任意文件名)，仅指定目录的情况时，会生成指定的目录名，并在该路径下保存为'__FOX_XUNIQ__'。
通常不需要设置。

## 设置案例

以下是AndroidManifest.xml的设置案例

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

在上述案例中，文件保存到以下路径

```
用Environment.getExternalStorageDirectory().getPath()获取的路径/fox_id_dir/fox_id_file
```

## 停止使用外部储存

想要通过Force Operation X SDK停止访问外部储存时，请在AndroidManifest.xml中添加APPADFORCE_USE_EXTERNAL_STORAGE的设置。```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

通过本项设置可以停止对外部储存的记录，但删除APP时会清空数据，可能导致无法进行准确计测。

## 关于Android M(6.0)的注意点

使用protectionLevel为dangerous且需要权限的功能时，必须得到用户许可。 用户拒绝时，数据将无法保存在外部存储区域进而无法进行排除重复设置。之前说到的`READ_EXTERNAL_STORAGE`和`WRITE_EXTERNAL_STORAGE`也为dangerous，需获得用户许可。

* [实际安装的参考](https://developer.android.com/training/permissions/requesting.html#perm-request)

[实际安装案例]
```java
package cyberz.jp.co.test;

import android.Manifest;
import android.annotation.TargetApi;
import android.content.pm.PackageManager;
import android.os.Build;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import co.cyberz.fox.Fox;

public class AndroidMMainActivity extends AppCompatActivity {

  private final int PERMISSION_REQUEST_CODE_FOX = 1;

  @TargetApi(Build.VERSION_CODES.M)
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

		// 在M的终端上实行时，Install计测前请用checkSelfPermission检查permissions
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
      if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
              && checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
        // 已经获取permission的情况
        Fox.trackInstall();
      } else {
        // 向用户要求permission处理
        requestPermissions(
                new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
                PERMISSION_REQUEST_CODE_FOX);
      }
    } else {
			// M以外的终端在安装时已经获取permission，直接执行install计测处理。
      Fox.trackInstall();
    }
  }

	@Override
  public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    switch (requestCode) {
      case PERMISSION_REQUEST_CODE_FOX:
        if (grantResults.length > 1
                && grantResults[0] == PackageManager.PERMISSION_GRANTED
                && grantResults[1] == PackageManager.PERMISSION_GRANTED) {
          // 获取用户许可执行install计测处理
          Fox.trackInstall();
        }
        break;
      default:
        break;
    }
  }
}
```

---
[Top](../../README.md)
