## アクセス解析による課金計測

アクセス解析機能を利用し、自然流入経由を含めた広告別の課金計測を行うことができます。アクセス解析による課金計測を行うために、次のsendEventメソッドを実装します。

```java
public static void sendEvent(Context context, String eventName,String action, String label, String orderId, String sku, String itemName, double price, int quantity, String currency);
```

sendEventメソッドのパラメータの仕様は下記の通りです。

|パラメータ|型|概要|
|:------|:------:|:------|
|eventName|String|トラッキングを行うイベントを識別できる任意の名前を設定します。イベント 名は自由に設定可能です。|
|action|String|イベントに属するアクション名を設定します。アクション名は自由に設定可能です。|
|label|String|アクションに属するラベル名を設定します。ラベル名は自由に設定可能です。nullでも構いません。|
|orderId|String|注文番号等を指定します。nullでも構いません。|
|sku|String|商品コード等を指定します。nullでも構いません。|
|itemName|String|商品名を指定します。nullでも構いません。|
|price|double|商品単価を指定します。|
|quantity|int|数量を指定します。price * quantityが売上金額として計上されます。|
|currency|String|通貨コードを指定します。nullの場合は"JPY"が指定されます。|


LTV計測においても課金を成果地点としている場合には、同一の箇所にLTVとアクセス解析のそれぞれの計測処理を実装します。

サンプルとして、以下にアメリカドルで9.99ドルの課金を行った場合の実装例を記載致します。

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

	//アプリ内課金成功時	public void payment(String orderId, String sku, String itemName, double price, int quantity) {
		//...
		// LTV計測による課金計測
		LtvManager ltv = new LtvManager(ad);
		ltv.addParam(LtvManager.URL_PARAM_PRICE, "9.99");
		ltv.addParam(LtvManager.URL_PARAM_CURRENCY, "USD");
		ltv.sendLtvConversion(成果地点ID);

		// アクセス解析による課金計測		AnalyticsManager.sendEvent(this, action, null, null, orderId, sku, itemName, 9.99, 1, "USD");	}}
```

---
[トップ](/lang/ja/README.md)
