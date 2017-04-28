## 액세스 해석에 의한 이벤트 계측

자연 유입 경유를 포함한 광고별로 유저가 앱 내에서 일으키는 이벤트를 측정 할 수 있습니다. 이벤트를 측정하고자하는 모든 지점에 코드를 추가합니다.

액세스 해석에 의한 이벤트 측정을 위해 다음 sendEvent 메소드를 구현합니다.

```java
import jp.appAdForce.android.AnalyticsManager;
```

다음 sendEvent 메소드를 이용하여 이벤트 정보를 보냅니다.

```java
public static void sendEvent(Context context, String eventName, String action, String label,
int value);
```

sendEvent 메소드의 파라미터의 사양은 아래와 같습니다.

|파라미터|형|개요|
|:------|:------|:------|
|eventName|String|계측하는 이벤트를 식별 할 수있는 모든 이름을 설정합니다. 이벤트 이름은 자유롭게 설정 가능합니다.|
|action|String|이벤트에 속하는 액션명을 설정합니다. 액션명은 자유롭게 설정 가능합니다. null이라도 상관없습니다.|
|label|String|이벤트에 속하는 레이블명을 설정합니다. 레이블명은 자유로게 설정 가능하며 null도 가능합니다.|
|value|int|이벤트 횟수를 지정합니다. 1이라도 상관 없습니다.|




```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

  //이벤트 성공시
  public void didTutorial()　{
    // 이벤트 송신
    AnalyticsManager.sendEvent("튜토리얼 돌파", null, null, 1);
  }
}
```

---
[TOP으로](/lang/ko/README.md)
