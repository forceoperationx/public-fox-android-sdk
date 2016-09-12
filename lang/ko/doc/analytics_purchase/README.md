## 액세스 해석에 의한 과금 계측

액세스 해석 기능을 이용하여 자연 유입 경유를 포함한 광고별 이벤트와 매출을 각각 측정 할 수 있습니다.
하기와 같이 sendEvent메소드를 구현해 주십시오.

```java
public static void sendEvent(Context context, String eventName,String action, String label, String orderId, String sku, String itemName, double price, int quantity, String currency);
```

sendEvent 메소드의 파라미터 사양은 하기와 같습니다.

|파라미터|형|개요|
|:------|:------:|:------|
|eventName|String|트래킹을 하는 이벤트를 식별 할 수있는 임의의 이름을 설정합니다. 이벤트명은 자유롭게 설정 가능합니다.゙|
|action|String|이벤트에 속하는 액션명을 설정합니다. 액션명은 자유롭게 설정 가능합니다.|
|label|String|액션에 속한 라벨명을 설정합니다. 라벨명은 자유롭게 설정 가능합니다. 특히 지정이없는 경우에는 null이라도 상관 없습니다.|
|orderId|String|주문 번호 등을 지정합니다. 특히 지정이없는 경우에는 null이라도 상관 없습니다.|
|sku|String|상품 코드 등을 지정합니다. 특히 지정이없는 경우는 null이라도 상관 없습니다.|
|itemName|String|상품명을 지정합니다.null이라도 상관 없습니다.|
|price|double|상품 단가를 지정합니다.|
|quantity|int|수량을 지정합니다. price * quantity가 매출액으로 계상됩니다.|
|currency|String|통화 코드를 지정합니다. null의 경우 "JPY"가 지정됩니다.|

LTV 계측에서 과금을 성과 지점으로하는 경우에는 동일한 부분에 LTV와 액세스 해석의 각각의 측정 처리를 구현합니다.

샘플로 다음에 미국 달러로 9.99 달러 결제를 한 경우의 구현 예를 기재합니다.

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

	//앱내 과금 성공시	public void payment(String orderId, String sku, String itemName, double price, int quantity) {
		//...
		// LTV 계측에 의한 과금 계측
		LtvManager ltv = new LtvManager(ad);
		ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
		ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
		ltv.sendLtvConversion(成果地点ID);

		// 액세스 분석에 의한 과금 계측		AnalyticsManager.sendEvent(this, action, null, null, orderId, sku, itemName, 9.99, 1, "USD");	}}
```

---
[TOP으로](/lang/ko/README.md)
