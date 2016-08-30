## 최신 버전으로 업데이트에 대하여

이전 버전의 F.O.X SDK가 설치된 앱에 대한 최신 버전의 F.O.X SDK를 설치하는데 필요한 단계를 설명합니다.

1. 이전 버전의 AppAdForce.jar이 존재하면 프로젝트에서 삭제
2.「2. SDK 설치 절차」에 따라서 최신 번전의 AppAdForce.jar를 프로젝트에 추가
3. Eclipse의 「프로젝트」→「클린」을 실행

※ 「[(옵션)광고ID를 이용하기 위한 Google Play Services SDK의 설치](../../google_play_services/README.md)」를 미실시한 경우에는 대응해 주세요.

※ 「[(옵션)외부 스토리지를 이용한 중복 배제 설정](../../external_storage/README.md)」를 미실시한 경우에는 대응해 주세요.

SDK의 업데이트후는, 반드시 효과측정 테스트를 실시하고, 계측 및 앱의 동작에 문제 없는 것을 확인해 주세요.

## 2.10.4g 버전에서 업데이트 된 점

Android SDK V2.10.4g 이전의 해외판/글로벌 판 SDK (버전 번호의 끝이 'u' 또는 'g')로부터 SDK를 최신판에 업데이트를 할 경우, ID의 하위호환성을 위해서, 다음과 같이 AdManger인스턴스를 생성하는 것 보다
이전의 위치에서 반드시 updateFrom2_10_4g()을 호출해 주십시오.

본 메소드에 의해, V2.10.4g 이전에 인스톨된 단말에 있어서, LTV나 액세스 해석의 계측을 계속할 수 있게 됩니다.

```java
AdManager.updateFrom2_10_4g(); // 반드시 new AdManager 보다 앞에서 호출할 것
AdManager ad = new AdManager(this);
ad.sendConversion("default");
```

> ※V2.10.4g보다 이전 버전의 SDK를 설치해 릴리즈된 적이 있는 앱, 예를 들면 V2.10g→V2.10.5g→V2.10.6g와 V2.10.4g보다 아래 버전의 SDK로부터 업데이트 할 때에도 updateFrom2_10_4g의 구현을 넣어 주십시오.

하기 버전의 SDK가 과거에 설치된 적이 있는 앱에서는 본 절차에 따를 필요가 있습니다.

* v2.7u / v2.7.1u / v2.7.2u
* v2.8u / v2.8.1u
* v2.9g / v2.9.1g / v2.9.2g
* v2.10g / v2.10.1g / v2.10.4g

> ※末尾に u や g がつかないバージョンからのアップデートの場合には本手順は必要ありません。
> ※버전의 끝에  'u' 나  'g'가 붙어 있지 않은 버전으로부터의 업데이트의 경우에는 본 절차를 따를 필요가 없습니다.

## v.2.14.6g 버전에서 업데이트 된 점

「[인스톨 계측의 구현](3.x/lang/ko/doc/send_conversion/README.md)」에 있어서, V2.14.7g 버전 부터 URL 스키마 경유로의 앱 기동시의 성과계측에 이용하는 메소드명을 아래와같이 변경했습니다.


<table>
  <tr>
    <td>변경후</td>
    <td>sendReengagementConversion(Intent)</td>
  </tr>
  <tr>
    <td>변경전</td>
    <td>setUrlScheme(Intenet)</td>
  </tr>
</table>

만일 setUrlScheme메소드를 이용해서 구현하고 있을 경우는, sendReengagementConversion메소드를 호출하게 수정해 주세요.

> ※현재 버전에서도 setUrlScheme메소드는 이용가능지만, 앞으로의 버전 업(version up)으로 삭제될 가능성이 있습니다. 한편, sendReengagementConversion 메소드와 setUrlScheme메소드의 내부처리는 동일해졌습니다.

---
[TOP으로](/3.x/lang/ko/README.md)
