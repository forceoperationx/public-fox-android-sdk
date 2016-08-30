## 외부 스토리지를 이용한 중복 배제 설정

앱의 첫 기동시에 SDK가 생성 한 식별 ID를 로컬 저장소 또는 SD 카드에 저장함으로써 앱의 재설치시 중복 판정을 할 수 있습니다.

이 설정은 필수는 아니지만 앱의 재설치 판정(중복 검사)의 정확도가 크게 향상하되기 때문에 구현을 권장하고 있습니다.

### 권한(퍼미션) 설정

외부 스토리지에 파일 읽기 및 쓰기에 필요한 권한 설정을 AndroidManifest.xml의 <manifest> 태그에 추가합니다.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

상기와 같이 권한이 설정되어있는 경우 다음과 같은 경로에 식별 ID 파일이 저장됩니다.

```
Environment.getExternalStorageDirectory().getPath()에서 얻을 수있는 경로 /앱의 패키지명/__FOX_XUNIQ__
```

### （임의）저장 디렉토리 및 파일 이름 변경

저장되는 파일의 디렉토리명은 표준에서는 패키지명으로 작성되지만, <application> 태그에 다음과 같이 설정을 추가하여 임의의 디렉토리 이름 및 파일 이름 변경 될 수 있습니다.

```xml
<meta-data android:name="APPADFORCE_ID_DIR" android:value="임의의 디렉토리명" />
<meta-data android:name="APPADFORCE_ID_FILE" android:value="임의의 파일명" />
```

> 임의의 디렉토리 나 파일명을 지정한 경우에도 Environment.getExternalStorageDirectory().getPath()의 반환 값의 경로 아래에 작성합니다.
 Environment.getExternalStorageDirectory().getPath()의 반환 값은 단말기와 OS 버전에 따라 다릅니다. <br>

> "APPADFORCE_ID_DIR"(임의의 디렉토리명)을 지정하지 않고 임의의 파일명만 지정하면 앱의 패키지 명의 디렉토리가 생성되고 그 아래에 임의의 파일명으로 저장됩니다. <br>

> ※ "APPADFORCE_ID_FILE"(임의의 파일명)을 지정하지 않고 임의의 디렉토리명 만을 지정했을 경우, 임의의 이름으로 디렉토리가 생성되고 그 아래에 "__FOX_XUNIQ__"에 저장됩니다. <br>
일반적으로 설정할 필요는 없습니다.


### 설정 예

하기는 AndroidManifest.xml의 설정예 입니다.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application
	android:icon="@drawable/ic_launcher"
	android:label="@string/app_name" >

	<meta-data android:name="APPADFORCE_ID_DIR" android:value="fox_id_dir" />
	<meta-data android:name="APPADFORCE_ID_FILE" android:value="fox_id_file" />

</application>

```

상기의 예제의 경우에 저장되는 파일의 경로는 다음과 같습니다.

```
Environment.getExternalStorageDirectory().getPath()에서 취득한 경로/fox_id_dir/fox_id_file
```

### 외부 스토리지 이용 정지

Force Operation X SDK 의한 외부 스토리지에 대한 액세스를 중지하려는 경우에는 AndroidManifest.xml에 APPADFORCE_USE_EXTERNAL_STORAGE 설정을 추가하십시오.

```xml
<meta-data android:name="APPADFORCE_USE_EXTERNAL_STORAGE" android:value="0" />
```

본 설정을 함으로써 외부 스토리지에 대한 기록이 중지되지만, 앱을 제거하면 데이터가 항상 초기화되기 때문에 정확한 인스톨 측정을 할 수 없게 됩니다.


### Android M(6.0)에서의 주의점

protectionLevel이 dangerous에 지정되어있는 권한을 필요로하는 기능을 이용하려면 사용자의 허가가 필요합니다.

사용자의 권한이 없을 경우 저장 공간에 데이터 저장이 되지 않기 때문에 중복 제거 설정을 사용할 수 없습니다.

앞에서 설명한`READ_EXTERNAL_STORAGE`과 `WRITE_EXTERNAL_STORAGE`에서도 dangerous로 되어 있어, 사용자에게 허가를 받기 위해 구현해야합니다.

* [구현의 참고](https://developer.android.com/training/permissions/requesting.html#perm-request)

[구현예]
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

		// M버전의 단말기에서 실행할 때 인스톨 측정 전에 checkSelfPermission에서 permissions을 체크한다
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
      if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
              && checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
        // 이미 퍼미션을 취득한 경우
        sendFoxConversion();
      } else {
        // 유저에게 퍼미션을 요구하는 처리
        requestPermissions(
                new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
                PERMISSION_REQUEST_CODE_FOX);
      }
    } else {
			// M버전 이외의 단말은 install시 permission을 얻을 수 있기 때문에 즉시 install 측정 처리를 실행
      sendFoxConversion();
    }
  }

	/**
	 * F.O.X SDK로 인스톨 측정 처리 실행 메소드
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
					// 유저의 승인을 얻어 install 측정 작업을 실행
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
[TOP으로](/3.x/lang/ko/README.md)
