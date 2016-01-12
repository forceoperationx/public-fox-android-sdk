## 依靠流量分析進行消費計測

利用流量分析機能，能夠計測不同廣告流入和自然流入的用戶消費狀況。為了依靠流量分析進行消費計測，請安裝下面的sendEvent方法。

```java
public static void sendEvent(Context context, String eventName,String action, String label, String orderId, String sku, String itemName, double price, int quantity, String currency);
```

sendEvent方法的參數說明如下。

|參數|型|概要|
|:------|:------:|:------|
|eventName|String|設定能夠識別監測Event的任意名稱。可以自由設定。|
|action|String|設定屬於Event的Action名。可以自由設定。|
|label|String|屬於Action的Label名。可以自由設定。不做特別指定的場合可以為null。|
|orderId|String|指定訂單號。不做特別指定的場合可以為null。|
|sku|String|指定商品代號sku。不做特別指定的場合可以為null。|
|itemName|String|指定商品名。可以為null。|
|price|double|指定商品單價。|
|quantity|int|指定數量。按price * quantity的銷售金額來計算在內。|
|currency|String|指定貨幣代碼。null的場合默認指定為"JPY"。|

如果希望在LTV計測地點也做消費計測，請在同一個地點安裝LTV和流量分析的各自的計測處理代碼。

サンプルとして、以下にアメリカドルで9.99ドルの課金を行った場合の実装例を記載致します。
下面是一個計測消費9.99美元的安裝實例。

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

	//APP内消費成功時	public void payment(String orderId, String sku, String itemName, double price, int quantity) {
		//...
		// LTV計測形式的消費計測
		LtvManager ltv = new LtvManager(ad);
		ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
		ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
		ltv.sendLtvConversion(成果地点ID);

		// 流量分析形式的消費計測		AnalyticsManager.sendEvent(this, action, null, null, orderId, sku, itemName, 9.99, 1, "USD");	}}
```

---
[TOP](/lang/tw/README.md)
