# F.O.X Reengagement廣告投放

## 1. 概要
本章對基於Force Operation X SDK(下面略稱F.O.X)的F.O.X Reengagement廣告投放以及和Criteo協作時的必要安裝做說明。
在進行F.O.X Reengagement廣告投放時，需要在相同地點安裝LTV和流量分析各自的計測處理。

* **對應F.O.X Android SDK版本** : `v2.16g`及以上

### 1.1.	SDK式樣

通過利用F.O.X SDK流量分析機能，進行橫跨媒體的協作Event計測。計測是依據內容來執行各種方法。

#### Event信息的送信

引入AnalyticsManager類庫。
```java
import jp.appAdForce.android.AnalyticsManager;
```

利用下面的sendEvent方法，發送Event信息。
```java
public static void sendEvent(Context context,
							 String eventName,
							 String action,
							 String label,
							 String orderId,
							 String sku,
							 String itemName,
							 double price,
							 int quantity,
							 String currency,
							 JSONObject eventInfo);
```

### sendEvent方法參數

各參數的式樣如下。

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventName|String|針對做計測的Event種別來設定指定的Event名稱。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。 |
|<span style="color:grey">label	|<span style="color:grey">String|<span style="color:grey">不使用。|
|orderId|String|(任意)指定訂單號。|
|sku	|String|(任意)指定商品代號sku。|
|<span style="color:grey">itemName|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">不使用。|
|price|double|	指定銷售額。|
|quantity|int|	指定數量。<br>按price * quantity來計算銷售額。|
|currency|String|指定貨幣代碼。null的場合默認指定為"JPY"。|
|eventInfo|JSONObject|按下面式樣的說明來指定Json。|

### 1.2. eventInfo JSON 佈局詳細
　在eventInfo裡，通過Json形式設定附帶信息給Action的方式，能夠進行動態廣告協作投放。Json式樣如下。

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|product|Array|閱覽過的放入購物車的商品的設定領域。|
|&nbsp;&nbsp;product[].id|String|指定閱覽過的放入購物車的商品ID。|
|&nbsp;&nbsp;product[].price|double|指定閱覽過的放入購物車的商品單價。|
|&nbsp;&nbsp;product[].quantity|int|指定閱覽過的放入購物車的商品數量。|
|&nbsp;&nbsp;product[].category|String|指定閱覽過的放入購物車的商品種別。<br>若想設定多個請用「,」來區分、分層次請用「>」來分割。<br>例）電影，錄像>DVD>體育，休閑|
|din|String|若想指定開始日請設定。|
|dout|String|若想指定結束日請設定。|
|criteo_partner_id|String|Criteo帳號ID在同一個APP裡不一樣的時候請設定。|
|fox_cvpoint|Long|輸入F.O.X的成果地點ID。|
|datafeed|JSONObject|實時數據字段(參考下方)|

### 1.3. 實時數據字段更新式樣
　在eventInfo裡，通過Json形式設定數據字段的更新情報的方式，根据F.O.X Reengagement廣告投放協作能夠實時更新數據字段。

#### JSON 佈局詳細

對於更新數據字段狀態的Action，請按下面Json式樣來安裝。

| 參數 |必須|型 | 概要 |
|:----------|:-------:|:----:|:------------|
|version|必須|String|指定數據字段的版本。|
|product|必須|Array|商品マスタデータフィードの設定領域です。|
|&nbsp;&nbsp;product[].id|必須|String|データフィードの商品を一意に識別するIDです。|
|&nbsp;&nbsp;product[].action|必須|String|データフィードをどのように変更するかを入力します。<br>U:追加もしくは編集　D:削除|
|&nbsp;&nbsp;product[].name|必須|String|商品名です。<br>削除の際はnullで構いません。|
|&nbsp;&nbsp;product[].expire|任意|String|商品の有効期限です。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。|
|&nbsp;&nbsp;product[].effective|任意|String|商品の公開日時です。<br>これが設定された場合、公開日時になるまで商品は表示されません。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。|
|&nbsp;&nbsp;product[].img|任意|String|商品画像のURLです。|
|&nbsp;&nbsp;product[].category1|任意|String|第一階層のカテゴリを指定します。|
|&nbsp;&nbsp;product[].category2|任意|String|第二階層のカテゴリを指定します。|
|&nbsp;&nbsp;product[].category3|任意|String|第三階層のカテゴリを指定します。|
|&nbsp;&nbsp;product[].price|任意|Double|商品の価格を指定します。|
|&nbsp;&nbsp;product[].currency|任意|String|商品の通貨コードを指定します。<br>nullの場合JPYが適用されます。|
|&nbsp;&nbsp;product[].(任意)|任意|String|その他の配信や分析に使用する項目を指定します。<br>データフィードの項目を設定してください。|


　　　　
## 2.イベント計測の実装
　F.O.X SDKで対応しているF.O.XエンゲージメントおよびCriteoのイベント計測は以下の５つとなっています。<br>実装の詳細は以下詳細ページにてご確認ください。

* [> View Toppage イベント](./ViewToppageEvent.md)
* [> View Listing イベント](./ViewListingEvent.md)
* [> View Product イベント](./ViewProductEvent.md)
* [> View Basket イベント](./ViewBasketEvent.md)
* [> Track Transaction イベント](./ViewTransactionEvent.md)


---
[トップ](/lang/ja/README.md)
