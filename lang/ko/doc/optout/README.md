## （옵션）OPT-OUT의 구현
광고 회사에 따라서는 타케팅 광고에 이용되지 않는 것을 유저가 선택하는 것이 가능합니다. 앱의 기동시의
개인 정보 보호 정책, 이용 규약을 표시하는 다이얼로그에서 유저가 OPT-OUT를 선택한 경우, 효과 계측의 결과의 통지와 함께 F.O.X가 광고회사에게 그 유저가 OPT-OUT를 선택한 것을 통지합니다.

OPT-OUT에 대응할 경우에는, 밑과 같이「[인스톨 계측의 구현](/lang/ko/doc/send_conversion/README.md)」에서 구현한 sendConversion 보다 전에 구현해 주십시오.

```java
AdManager ad = new AdManager(this);
// 유저가 OPT-OUT를 선택한 경우에 setOptout를 유효화한다.if(user.optout) {	ad.setOptout(true);}
ad.sendConversion("default");
```

---
[TOP으로](/lang/ko/README.md)
