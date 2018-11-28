[TOP](../../README.md)　>　De-duplication feature settings (external storage permissions)

---

# De-duplication feature settings (external storage permissions)

External storage permissions are used to read and write data to external storage. Having these permissions enabled allows F.O.X's de-duplication feature to function optimally.

While these permissions are not necessary for the SDK to function, because it allows F.O.X to track app reinstall with greater accuracy, we recommend you enable these settings in your app.

## Permission Settings

The F.O.X SDK utilizes the following two permissions.
Add them inside your `Manifest` tags.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

By having the above two permissions set, a unique ID can be saved in the following path.

```
Path_acquired_by_Environment.getExternalStorageDirectory().getPath()/App_Package_Name/__FOX_XUNIQ__
```

## Directory and Filename Settings (Optional)

The filename and directory name are set by the package name by default, but you may manually select them by including the following within your `Application` tag.

```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="yourdirectoryname" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="yourfilename" />
```
> Even when manually selecting a directory/filename, they will be stored under the path designated by the return value of Environment.getExternalStorageDirectory().getPath(). This returned path can change depending on the version of your OS.<br>

> If you do not explicitly set a directory name value, setting only a filename, a default directory named after your package name will be created.

> If you do not explicitly set a file name value, setting only a directory name, a default file named "__FOX_XUNIQ__" will be created.
Typically, these settings are not necessary.

## Example Settings

Here is an example `AndroidManifest.xml`

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

If you use the above example source, the file's save path will be as follows.

```
Path_acquired_by_Environment.getExternalStorageDirectory().getPath()/fox_id_dir/fox_id_file
```

## Disabling External Storage Use

If you want to prevent F.O.X from accessing external storage, please set `APPADFORCE_USE_EXTERNAL_STORAGE` as follows.

```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

By establishing the above settings you can stop the use of external storage, but any data recorded up until then will persist. Because of this, the data will always be loaded, and you will not be able to perform install tracking.

## Warnings for Android M(6.0)

Beginning with Android Marshmallow(6.0), in order to use permissions with the ProtectionLevel of `dangerous`, users must accept those permissions at a prompt.
If the app cannot obtain user permissions, F.O.X cannot save the appropriate data to external storage, thus lowering the accuracy of the de-duplication feature.
The two permissions`READ_EXTERNAL_STORAGE`と`WRITE_EXTERNAL_STORAGE` written above are designated as `dangerous` and therefore must be accepted at a user prompt. <br>
The settings for this are as follows.

* [Android Developers Reference](https://developer.android.com/training/permissions/requesting.html#perm-request)

[Example]
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

    // When running on a Marsmallow device, the checkSelfPermission method checks permissions before install tracking
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
      if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
              && checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
        // if permissions are already granted
        Fox.trackInstall();
      } else {
        // request user permissions
        requestPermissions(
                new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
                PERMISSION_REQUEST_CODE_FOX);
      }
    } else {
      // Permissions for devices besides Marshmallow are granted at install time, so we perform install tracking
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
          // gain user permissions and perform install tracking
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
[Return to Top](../../README.md)
