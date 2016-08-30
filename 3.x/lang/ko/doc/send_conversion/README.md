## sendConversion의 상세

sendConversion 메소드를 이용하여 설치 측정을 할 수 있습니다. Cookie 측정을 이용하는 경우에는 외부 브라우저가 시작됩니다. 이 때 외부 브라우저가 이동할 URL을 sendConversion 인수에 URL 문자열을 지정할 수 있습니다.

프로젝트의 소스 코드를 편집하고 앱이 시작될 때 호출되는 Activity에 대해 다음과 같이 구현을해야합니다.

> 【주의】
sendConversion는 특별한 이유가없는 한 앱이 시작될 때 호출되는 Activity의 onCreate 내에 구현하십시오. 그외의 부분에 구현 된 경우에는 인스톨 수가 정확하게 측정되지 않을 수 있습니다.
앱이 시작될 때 호출되는 Activity의 onCreate 내에 구현하지 않은 상태에서 인스톨 성과 형의 광고를 실시하는 경우에는 반드시 광고 대행사 또는 매체사의 담당자에게 그 사실을 알려주십시오.
 정확하게 측정 할 수없는 상태에서 인스톨 성과 형의 광고를 실시 된 때 측정 된 인스톨 횟수 이상의 광고비 지불을 요구하는 우려가 있습니다.

```java
import jp.appAdForce.android.AdManager;

@Override
public void onCreate(Bundle savedInstanceState) {
	AdManager ad = new AdManager(this);
	ad.sendConversion("default");
}
```

sendConversion의 인수에는 일반적으로 위와 같이 "default"라는 문자열을 입력하십시오. 디폴트일 경우는 표준으로 준비된 페이지가 표시되지만 이동할 곳의 URL은 FOX 관리 화면에서 임의로 설정할 수도 있습니다.

![sendConversion01](./img01.png)

특정 URL으로 전환시키고 싶은 경우 나 앱에서 동적으로 URL을 생성하고자하는 경우에는 URL 문자열을 설정하십시오.

```java
ad.sendConversion("http://yourhost.com/yourpage.html");
```

sendConversion 메서드의 두 번째 매개변수에 광고주 단말 ID를 전달할 수 있습니다. 예를 들어, 앱 시작 시 UUID를 생성하고 첫 기동의 성과와 관련지어 관리하고 싶은 경우 등에 사용할 수 있습니다.

```java
ad.sendConversion("default", "your unique id");
```
> sendConversion는 시작 직후의 처리로 구현 될 필요가 있기 때문에, 로그인 ID 등의 사용자 작업이 수반 값을 매개변수로 전달할 수 없습니다.

---
[Top으로](/3.x/lang/ko/README.md)
