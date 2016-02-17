# Force Operation X 란?

Force Operation X (이하 F.O.X)는 스마트폰의 광고 효과 최적화를 위한 토탈 솔루션 플랫폼 입니다. 앱의 다운로드, 웹상에서의 사용자 액션의 측정은 물론, 스마트폰 사용자의 행동 특성에 근거한 독자적인 효과측정기준을 바탕으로 기업의 프로모션의 비용효과를 극대화 할 수 있습니다.

본 문서에서는 스마트폰 앱의 광고 효과 극대화를 위한 F.O.X SDK 설치 단계에 대해 설명합니다.

## F.O.X SDK 란?

F.O.X SDK를 앱에 설치함으로써 아래와 같은 기능을 제공합니다.

* **설치 계측**

광고 유입별 설치 횟수를 측정 할 수 있습니다.

* **LTV 측정**

유입원 광고별로 Life Time Value를 측정합니다. 주요 성과 지점으로는 회원 가입, 튜토리얼 돌파, 결제 등이 있습니다. 각 광고별 등록률, 과금률, 과금액 등을 측정 할 수 있습니다.

* **액세스 해석**

자연 유입과 광고 유입의 설치 비교, 앱의 기동수, 유니크 사용자수(DAU/MAU), 지속률 등을 측정 할 수 있습니다.

## 1. 설치

아래의 페이지에서 최신의 SDK를 다운로드 하십시오.

* [SDK 릴리스 페이지](https://github.com/cyber-z/public_fox_android_sdk/releases)

이미 앱에 SDK가 설치되어있는 경우에는, [최신 버전에 대한 업데이트에 대해](./doc/update/README.md)를 참조하십시오.

다운로드한 SDK 「FOX_Android_SDK_<version>.zip」의 압축을 풀고, 「AppAdForce.jar」을 앱의 프로젝트에 추가해 주십시오.

* [Eclipse 프로젝트에 추가하는 방법](./doc/integration/eclipse/README.md)
* [Android Studio 프로젝트에 설치하는 방법](./doc/integration/android_studio/README.md)



## 2. 설정


* **SDK 설정**

SDK의 동작에 필요한 설정을 AndroidManifest.xml에 추가합니다.

### 권한(퍼미션) 설정

F.O.X SDK에서는 아래 4개의 퍼미션을 이용합니다.
&lt;Manifest&gt;태그내에 다음과 같이 퍼미션의 설정을 추가합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

퍼미션|Protection Level|필수|개요
:---|:---:|:---:|:---
INTERNET|Normal|필수|F.O.X SDK가 통신을 하기 위해서 필요합니다.
ACCESS_NETWORK_STATE|Normal|필수|F.O.X SDK가 통신 가능 상태인가를 확인하기 위해서 필요합니다.
READ_EXTERNAL_STORAGE ※1|Dangerous|임의|스토리지를 이용한 중복 배제 기능 향상에 필요합니다. (※2)
WRITE_EXTERNAL_STORAGE ※1|Dangerous|임의|스토리지를 이용한 중복 배제 기능 향상에 필요합니다. (※2)

> ※1 READ_EXTERNAL_STORAGE 및 WRITE_EXTERNAL_STORAGE 퍼미션은, 외부 스토리지에 데이터를 기록하는 것으로 앱의 재인스톨시에 정확하게 인스톨 계측을 하기 위해서 필요합니다만, 필수는 아닙니다.

> ※2 Android M버전 부터는 ProtectionLevel이 'dangerous'로 지정되어 있는 퍼미션을 필요로 하는 기능을 이용하기 위해서는, 사용자의 허가가 필요합니다.
 상세한 것은 [외부 스토리지를 이용한 중복 배제 설정](./doc/external_storage/README.md)을 확인해 주세요.

### 메타 데이터 설정

SDK의 실행에 필요한 정보를 <application> 태그에 추가합니다.

```xml
<meta-data
	android:name="APPADFORCE_APP_ID"
	android:value="Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오." />
<meta-data
	android:name="APPADFORCE_SERVER_URL"
	android:value="Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오." />
<meta-data
	android:name="APPADFORCE_CRYPTO_SALT"
	android:value="Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오." />
<meta-data
	android:name="ANALYTICS_APP_KEY"
	android:value="Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오." />
```

설정 키와 값은 다음과 같습니다.

|파라미터명|필수|개요|
|:------|:------|:------|
|APPADFORCE_APP_ID|필수|Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오.|
|APPADFORCE_SERVER_URL|필수|Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오.|
|APPADFORCE_CRYPTO_SALT|필수|Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오.|
|ANALYTICS_APP_KEY|필수|Force Operation X 관리자가 연락합니다. 그 값을 입력하십시오.|


### 인스톨 리퍼러(Install Referrer) 계측의 설정
인스톨 리퍼러를 이용한 설치 측정을 위해 아래와 같은 설정을 <application> 태그에 추가합니다.

```xml
<receiver android:name="jp.appAdForce.android.InstallReceiver" android:exported="true">
	<intent-filter>
		<action android:name="com.android.vending.INSTALL_REFERRER" />
	</intent-filter>
</receiver>
```

이미 "com.android.vending.INSTALL_REFERRER"에 대한 리시버 클래스가 정의되어있는 경우,[두개의 INSTALL_REFERRER 리시버를 공유시키는 경우의 설정](./doc/install_referrer/README.md)를 참조해 주십시오.

### URL스키마의 설정

앱을 외부에서 기동될 수 있도록 대상의 &lt;activity&gt; 태그에 아래와 같은 설정을 추가하십시오.


```xml
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
	<data android:scheme="임의의 URL 스키마를 기입해 주십시오" />
</intent-filter>
```

> 환경에 따라서는 URL 스키마의 대소 문자를 구별하지 않음으로써 정상적으로 URL 스키마의 전환이 되지 않는 경우가 있습니다. URL 스키마는 모두 소문자 영숫자를 사용하여 설정해 주십시오.

### 광고 ID를 이용하기위한 Google Play Services SDK의 설치

광고 ID를 이용하기 위해서는 Google Play Services SDK가 설치되어 있어야합니다. Google Play Services SDK가 설치되어 있지 않은 경우에도 FOX SDK는 작동하지만,
광고 ID가 이용되지 않는 것으로 일부 광고 메뉴의 계측을 할 수 없거나, 중복 판정의 정확도가 저하합니다. 구현에 대한 자세한 절차는
[광고 ID를 이용하기위한 Google Play Services SDK의 설치](./doc/google_play_services/README.md)를 참조해 주십시오.


### AndroidManifest.xml 샘플

[AndroidManifest.xml 샘플](./doc/config_android_manifest/AndroidManifest.xml)


## 3. 인스톨 계측의 구현

첫 기동의 인스톨 계측을 구현하는 것으로, 광고의 효과 측정을 할 수 있습니다. 프로젝트의 소스 코드를 편집하고 다음과 같이 구현을 해야합니다.

앱이 시작될 때 호출되는 Activity의 onCreate()안에 sendConversion 메소드를 구현합니다.

```java
import jp.appAdForce.android.AdManager;

@Override
protected void onCreate(Bundle savedInstanceState){
	super.onCreate(savedInstanceState);

	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}
```

sendConversion의 매개변수에는, 특별한 이유가 없을 때에는 @"default"를 넣어 주십시오.

[sendConversion의 상세](./doc/send_conversion/README.md)

또 재반응(re-engagement)유도 계측 (URL 스키마 경유 기동 측정)하기 위해, `URL 스키마가 설정되어있는 모든 Activity(※ 1)의 `onResume()`에 `sendReengagementConversion` 메소드를 구현합니다.


```java
import jp.appAdForce.android.AdManager;

@Override
protected void onResume() {
	super.onResume();
	AdManager ad = new AdManager(this);
	ad.sendReengagementConversion(getIntent());
}
```

URL 스키마로 기동되는 Activity의 launchMode가 "singleTask" 또는 "singleInstance"의 경우, URL 스키마 경유로 매개 변수를 받기 위하여 `onNewIntent` 메소드를 override하여 다음과 같이 `setIntent` 메소드를 호출해 주십시오.

```java
@Override
protected void onNewIntent(Intent intent)
{
	super.onNewIntent(intent);
	setIntent(intent);
}
```

> ※1 재반응(re-engagement)유도 계측을 위해서는 AndroidManifest.xml에 정의되어있는 Acitvity에 사용자 정의 URL 스키마가 설정되어 있어야합니다. 본 측정에서는 사용자 정의 URL 스키마에 의해 Activity가 호출되는 것으로 재반응(re-engagement)유도 계측합니다.

## 4. LTV 계측의 구현

회원등록, 튜토리얼 돌파, 과금 등의 임의의 성과 지점에 LTV 계측을 구현하는 것으로, 유입원 광고의 LTV를 계측할 수 있습니다.
LTV 계측이 불필요한 경우에는, 본 구현을 생략해 주십시오.

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(성과지점ID);
```

LTV 계측을 하기 위해서는, 각 성과 지점을 식별하는 성과지점 ID를 지정할 필요가 있습니다. `sendLtvConversion`의 매개변수에 발행된 ID를 지정해 주십시오.

과금 계측을 하는 경우에는, 과금이 완료된 장소에 하기와 같이 과금액과 통화코드를 지정해 주십시오.

```java
import jp.appAdForce.android.LtvManager;
// ...
LtvManager ltv = new LtvManager(ad);
ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
ltv.sendLtvConversion(성과지점ID);
```

LtvManager.URL_PARAM_CURRENCYには[ISO 4217](https://ko.wikipedia.org/wiki/ISO_4217)에 정의된 통화 코드를 지정해 주십시오.

[태그를 이용한 LTV 계측에 대해서](./doc/ltv_browser/README.md)

## 5. 액세스 해석의 구현

자연 유입과 광고 유입의 인스톨 수 비교, 앱의 기동 수, 유니크 유저수(DAU/MAU), 유지율 등을 계측하는 것이 가능합니다. 액세스 해석이 불필요한 경우에는, 본 구현을 생략해도 됩니다.

앱의 기동,및 백그라운드로부터의 복귀를 계측하기 위해서, 각 Activity의 onResume()에 `AnalyticsManager.sendStartSession` 메소드를 추가합니다.

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {
	@Override
	protected void onResume() {
		super.onResume();
		AdManager ad = new AdManager(this);
		ad.sendReengagementConversion(getIntent());

		AnalyticsManager.sendStartSession(this);
	}
}
```

> ※앱이 여러개의 Activity를 생성하는 경우에는 모든 Activity의 onResume()에 처리를 추가하십시오.
앱이 백그라운드에서 복귀했을 때 그 Activity에 기동 측정의 구현이 되어 있지 않은 경우 등 정확한 활성 사용자를 측정할 수 없게 됩니다.

[액세스 해석에 의한 이벤트 계측](./doc/analytics_event/README.md)

[액세스 해석에 의한 과금 계측](./doc/analytics_purchase/README.md)


## 6. ProGuard를 이용할 경우

ProGuard를 이용하여 앱의 코드 난독화 처리를 할 때는 F.O.X SDK의 메소드가 대상이되지 않도록 다음 설정을 추가하십시오.

```
-keepattributes *Annotation*

-libraryjars libs/AppAdForce.jar
-keep interface jp.appAdForce.** { *; }
-keep class jp.appAdForce.** { *; }
-keep class jp.co.dimage.** { *; }
-keep class com.google.android.gms.ads.identifier.* { *; }
-dontwarn jp.appAdForce.android.**
-dontwarn jp.co.dimage.**
-dontwarn jp.co.cyberz.fox.**
-dontwarn com.adobe.fre.FREContext
-dontwarn com.adobe.fre.FREExtension
-dontwarn com.adobe.fre.FREFunction
-dontwarn com.adobe.fre.FREObject
-dontwarn com.ansca.**
-dontwarn com.naef.jnlua.**
```

또한 Google Play Service SDK설치 되어있는 경우는 다음 페이지에 기재되어 keep 지정이 기술되어 있는지 확인하십시오.

[Google Play Services 설치시의 Proguard대응](https://developer.android.com/google/play-services/setup.html#Proguard)


## 7. 소통 테스트 실시

마켓에 신청하기 전까지 SDK를 설치한 상태에서 충분히 테스트하여 앱의 동작에 문제가 없는지 확인하십시오.

인스톨 계측의 통신은 앱 기동후에 한 번만 실행합니다. 계속해서 효과 계측 테스트를 실시하고 싶은 경우에는 앱을 제거하고 다시 설치 후 사용하십시오.

* **테스트 방법**

1. 테스트용 단말기에 테스트 앱이 설치되어있는 경우에는 제거
1. 테스트 용 단말기의 디폴트 브라우저의 Cookie를 삭제
1. 폐사에서 발행 한 테스트 용 URL을 클릭
1. 마켓에 리다이렉션
1. 테스트 용 단말기에 테스트 앱을 설치<br />
1. 앱을 실행, 브라우저가 시작<br />
여기에서 브라우저가 시작되지 않는 경우에는 정상적으로 설정이 이루어지고 있지 않습니다. 설정을 다시 살펴보신 후 문제가 없을 경우에는 폐사에 문의주십시오.
1. LTV 지점까지 화면 전환<br />
1. 앱을 종료하고 백그라운드에서 제거<br />
1. 다시 앱을 기동<br />

폐사에 3,6,7,9 시간을 알려주십시오. 제대로 측정이 이루어지고 있는지 확인합니다. 폐사 측의 확인에서 문제가 없다면 테스트 완료됩니다.

> 테스트용 URL은 반드시 단말기의 기본 브라우저에서 실행하여 주십시오. Chrome등의 3rd party 브라우저, 메일 앱, QR 코드 앱을 이용하여 WebView로 전환된 경우에는 계측되지 않습니다.

> 테스트 URL을 클릭했을 때 전환 대상이 없어 오류 메시지가 표시되는 경우가 있습니다만 소통 테스트에서는 문제 없습니다.


[재반응 유도(Re-engagement)계측을 하기 위한 테스트 순서](./doc/reengagement_test/README.md)


## 8. 기타 기능의 구현

* [OPT-OUT 구현](./doc/optout/README.md)


## 9. 마지막으로 반드시 확인하시기 바랍니다 (지금까지 발생한 문제들)

### 9.1. URL 스키마의 설정되지 않고 릴리스 되었기 때문에 브라우저에서 응용 프로그램으로 전환이 되지 않음

Cookie 계측으로 브라우저를 기동 한 경우에는 URL 스키마를 이용하여 응용 프로그램을 전환합니다. 이 때 독자의 URL 스키마가 설정되어 있어야합니다.

### 9.2. URL 스키마에 대문자가 포함되어 정상적으로 앱으로 전환되지 않음

환경에 따라 URL 스키마의 대소 문자가 판별되지 않음으로써 URL 스키마의 전환이 일어나지 않는 경우가 있습니다. URL 스키마는 모두 소문자로 설정해야 합니다.


### 9.3. F.O.X에서 확인할 수있다 인스톨 수치가 Google Play Developer Console의 숫자보다 크다
F.O.X는 몇가지 방식을 조합하여 단말의 중복 설치 감지하고 있습니다.
중복 감지 할 수 없는 설정은 동일 단말에서도 다시 설치될 때마다 F.O.X는 신규 설치로 판정해 버립니다.

중복 탐지의 정확성을 향상시키기 위해 다음과 같이 설정하십시오.

* [광고 ID를 이용하기위한 Google Play Services SDK의 설치](./doc/google_play_services/README.md)

* [외부 스토리지를 이용한 중복 제거 설정](./doc/external_storage/README.md)

* [Android M (6.0) 자동 백업 기능 이용](./doc/auto_backup/README.md)

---
[TOP 메뉴로](/README.md)
