## Android M (6.0) 자동 백업 기능 이용

Android M (6.0)버전 부터 추가 된 자동 백업을 이용하여 중복 제거 기능을 향상시킬 수 있습니다.(옵션)
본 대응은 필수는 아닙니다.

## 이용 가능 조건

* Android M 단말기부터 Android M 이상의 단말기에 백업 데이터를 복원한 경우입니다.
* 백업 시점과 복원시에 단말 로그인 한 Google 계정이 동일해야합니다.
* 데이터를 계승할 때에는 아래 그림과 같이 단말기 설정이 사용자에 의해 활성화되어있는 것이 필수입니다.

![설정화면](./img01.png)

## 설정

　이용시 앱 백업 설정 파일의 상황에 맞게 설정하실 필요가 있습니다.

> [참고 설정](https://developer.android.com/training/backup/autosyncapi.html)

**[백업 할 앱의 데이터를 개별적으로 지정하는 경우]**

　하기의 파일이 백업에 포함되도록 설정하십시오.

```
<include domain="file" path="__ADMAGE_RANDOM_DEVICE_ID__" />
```

**[전체 앱 데이터를 백업 할 경우]**

　앱 데이터 모두가 백업 대상이되도록 설정되어있는 경우 다음 파일은 제외되도록 설정하십시오.

```
<exclude domain="file" path="__ADMAGE_WEB_CONVERSION_COMPLETED__" />
<exclude domain="file" path="__ADMAGE_APP_CONVERSION_COMPLETED__" />
```

---
[TOP으로](/3.x/lang/ko/README.md)
