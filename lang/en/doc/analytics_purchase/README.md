## Purchase-event tracking by using access analytics

Using the access analysis function, it is possible to measure respectively events and sales both organic and non-organic user. To conduct event measurement and billing measurement by access analysis, implement next sendEvent method.

```java
public static void sendEvent(Context context, String eventName,String action, String label, String orderId, String sku, String itemName, double price, int quantity, String currency);
```

Specification of the arguments of the `sendEvent` is as follows.

|Parameter|Type|Outline|
|:------|:------:|:------|
|eventName|String|Set the arbitrary name identifying event conducting tracking. It is able to set event name freely.|
|action|String|Set the action name belonging to event. It is able to set action name freely. If not specifying, null is acceptable.|
|label|String|Set the label name belonging to action. It is able to set label name freely. If not specifying, null is acceptable.|
|orderId|String|Set the order ID, null is acceptable.|
|sku|String|Set the product code, null is acceptable.|
|itemName|String|Set the product name, null is acceptable.|
|price|double|Set the unit price|
|quantity|int|Set the amount. price * quantity is calculated as total sales.|
|currency|String|Set the currency code In the case of null, "JPY" is specified.|

> Specify the currency code defined by [ISO 4217](http://en.wikipedia.org/wiki/ISO_4217) for currency.

In the case of setting billing as result points in LTV measurement, implement measurement management of each LTV and access analysis at same place.

As a sample, the example of implementation is noted below and it is an example of 9.99 American dollars.

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

	public void payment(String orderId, String sku, String itemName, double price, int quantity) {
		//...

		// Billing measurement by LTV measurement
		LtvManager ltv = new LtvManager(ad);
		ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
		ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
		ltv.sendLtvConversion(LTV POINT ID);

		// billing measurement by access analytics
		AnalyticsManager.sendEvent(this, "In-App purchase", action, null, null, orderId, sku, itemName, 9.99, 1, "USD");
	}

}
```

---
[TOP](/lang/en/README.md)
