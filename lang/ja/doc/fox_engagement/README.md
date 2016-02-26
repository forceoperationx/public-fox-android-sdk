# F.O.Xエンゲージメント配信

## 1. 概要
　Force Operation X SDK(以下F.O.X)におけるF.O.Xエンゲージメント配信およびCriteoとの連携を行う際に必要となる実装を説明します。F.O.Xエンゲージメント配信連携を行う場合は同一の箇所にLTVとアクセス解析のそれぞれの計測処理を実装する必要があります。

* **対応F.O.X Android SDKバージョン** : `v2.16g`以上

### 1.1.	SDK仕様
　F.O.X SDKアクセス解析機能を利用することにより媒体を横断したイベント計測連携を行います。計測は内容に応じて各種メソッドを実行することで行います。

引数それぞれのパラメータの仕様は以下の通りです。

| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|計測を行うイベント種別に応じて、指定されたイベント名を設定します。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。 |
|<span style="color:grey">label	|<span style="color:grey">String|<span style="color:grey">使用しません。|
|orderId|String|(任意)注⽂番号等を指定します。|
|sku	|String|(任意)商品コード等を指定します。|
|<span style="color:grey">itemName|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|price|double|	売上金額を指定します。|
|quantity|int|	数量を指定します。<br>price * quantityが売上金額として計上されます。|
|currency|String|通貨コードを指定します。nullの場合は”JPY”が指定されます。|
|eventInfo|JSONObject|下記の仕様のとおりにJsonを指定します。|

### 1.2. eventInfo仕様
　eventInfo内にアクションに付随する情報をJson形式で設定することで、ダイナミックな配信連携が可能になります。
Jsonの仕様は以下の通りです。

| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|product[].id|String|閲覧した、カートに入れた等の商品IDを設定します。|
|product[].price|double|閲覧した、カートに入れた等の商品単価を設定します。|
|product[].quantity|int|閲覧した、カートに入れた等の商品数量を設定します。|
|product[].category|String|閲覧した、カートに入れた等の商品カテゴリを指定します。<br>複数ある場合はカンマ「,」区切り、階層がある場合は「>」で分割します。<br>例）映画、ビデオ>DVD>スポーツ、レジャー|
|din|String|開始日の指定がある場合に設定します。|
|dout|String|終了日の指定がある場合に指定します。|
|criteo_partner_id|String|CriteoアカウントIDが同一アプリで異なる場合に設定します。|
|fox_cvpoint|Long|F.O.Xの成果地点IDを入力します。|

### 1.3. リアルタイムデータフィード更新仕様
　eventInfo内にデータフィードの更新情報をJson形式で設定することで、F.O.Xエンゲージメント配信連携により、リアルタイムにデータフィードを更新することができます。

データフィードの状態が更新されるアクションの場合、下記を実装してください。

| 引数 |必須|型 | 概要 |
|:----------:|:-------:|:----:|:------------|
|datafeed.version|必須|String|データフィードのバージョンを指定します。|
|datafeed.product|必須|Array<Object>|商品マスタデータフィードの設定領域です。|
|datafeed.product[].id|必須|String|データフィードの商品を一意に識別するIDです。|
|datafeed.product[].action|必須|String|データフィードをどのように変更するかを入力します。<br>U:追加もしくは編集　D:削除|
|datafeed.product[].name|必須|String|商品名です。<br>削除の際はnullで構いません。|
|datafeed.product[].expire|任意|String|商品の有効期限です。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。|
|datafeed.product[].effective|任意|String|商品の公開日時です。<br>これが設定された場合、公開日時になるまで商品は表示されません。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。|
|datafeed.product[].img|任意|String|商品画像のURLです。|
|datafeed.product[].category1|任意|String|第一階層のカテゴリを指定します。|
|datafeed.product[].category2|任意|String|第二階層のカテゴリを指定します。|
|datafeed.product[].category3|任意|String|第三階層のカテゴリを指定します。|
|datafeed.product[].price|任意|Double|商品の価格を指定します。|
|datafeed.product[].currency|任意|String|商品の通貨コードを指定します。<br>nullの場合JPYが適用されます。|
|datafeed.product[].(任意)|任意|String|その他の配信や分析に使用する項目を指定します。<br>データフィードの項目を設定してください。|

### 1.4. イベント情報の送信方法

AnalyticsManagerクラスをインポートします。
```java
import jp.appAdForce.android.AnalyticsManager;
```

次のsendEventメソッドを利用し、イベント情報を送信します。
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
　　　　
## 2.イベント計測の実装
　F.O.X SDKで対応しているF.O.XエンゲージメントおよびCriteoのイベント計測は以下の５つとなっています。

- View Home イベント
- View Listing イベント
- View Product イベント
- View Basket イベント
- Track Transaction イベント

### 2.1	View Home(アプリトップ訪問イベント)実装方法
　View Home（ホーム画面）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

```java
JSONObject json = new JSONObject("{" +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'," +
                                 "'fox_cvpoint': '12345'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_toppage", null, null, 0, json);
```

#### 引数詳細
| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|“\_view\_toppage”を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo(din/dout)|JSONObject|日付の指定がある場合は入力（任意）|
|eventInfo(criteo_partner_id)|JSONObject|CriteoアカウントIDが同一アプリで異なる場合は入力(任意)|
|eventInfo(fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。(任意)|
　　
　　　　
### 2.2	View Listing（複数商品閲覧イベント）実装方法
　View Listing（検索結果・一覧画面）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。上位３つの商品が計測されます。

```java
JSONObject json = new JSONObject("{" +
                                 "'fox_cvpoint': '12345'," +
                                 "'product':[" +
                                        "{'id': '111'," +
                                        "'category':'映画、ビデオ>DVD>スポーツ、レジャー'}," +
                                        "{'id': '112'," +
                                         "'category':'映画、ビデオ>DVD>スポーツ、レジャー'}," +
                                        "{'id': '113'," +
                                         "'category':'映画、ビデオ>DVD>スポーツ、レジャー'}" +
                                 "]," +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_listing", null, null, 0, json);
```

#### 引数詳細
| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|"\_view\_listing"を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo(product)|JSONObject|Product をキーとして商品IDを配列で設定します。
|eventInfo(product[].id)|JSONObject|商品IDを設定します。<br>データフィードと同じ商品IDを使⽤してください。|
|eventInfo(product[].category)|JSONObject|商品カテゴリを設定します。<br>データフィードと同じ商品カテゴリを使用してください。<br>１商品に対して複数カテゴリある場合はカンマ「,」区切り、階層がある場合は「>」で分割します。<br>例）映画、ビデオ>DVD>スポーツ、レジャーnullでも構いません。|
|eventInfo(din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo(criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)|
|eventInfo(fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|

　　　　
### 2.3	View Product（商品閲覧イベント）実装方法
　View Product（単一商品閲覧）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

```java
JSONObject json = new JSONObject("{" +
                                 "'fox_cvpoint':12345," +
                                 "'product':[{'id': '111'}]," +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_content", null, null, 0, json);
```

#### 引数詳細
| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|"\_view\_content" を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo(fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|
|eventInfo(product[].id)|JSONObject|閲覧した商品IDを設定します。|
|eventInfo(din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo(criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)|
　　
　　　　
### 2.4	View Basket（買い物かご）実装方法
　View Basket（商品購入予定一覧）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

```java
JSONObject json = new JSONObject("{" +
                                 "'currency':'JPY'," +
                                 "'fox_cvpoint':12345," +
                                 "'product':[" +
                                            "{'id':'1234','price':100,'quantity':1}," +
                                            "{'id':'3456','price':200,'quantity':2}," +
                                            "{'id':'6789','price':300,'quantity':3}" +
                                           "]," +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_add_to_cart", null, null, 0, json);
```

#### 引数詳細
| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|"\_add\_to\_cart" を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo(fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|
|eventInfo(product[].id)|JSONObject|商品ID<br>データフィードと同じ商品IDを使用してください。|
|eventInfo(product[].price)|JSONObject|該当商品の価格を設定します。|
|eventInfo(product[].quantity)|JSONObject|該当商品を買った個数を設定します。|
|eventInfo(currency)|JSONObject|通貨<br>Nil/Nullの場合、デフォルト “JPY”|
|eventInfo(din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo(criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)|
　　
　　　　
### 2.5	Track Transaction（商品購入イベント）実装方法
Track Transaction（商品購入）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

```java
JSONObject json = new JSONObject("{" +
                                 "'fox_cvpoint':'12345'," +
                                 "'transaction_id':'ABCDFE’," +
                                 "'product':[" +
                                            "{'id':'1234','price':100,'quantity':1}," +
                                            "{'id':'1235','price':200,'quantity':2}," +
                                            "{'id':'1236','price':300,'quantity':3}" +
                                            "]," +
                                 "'din':'2016 -01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_purchase", null, null, null, null, null, 1400, 6, "JPY",　json);
```

#### 引数詳細
| 引数 | 型 | 概要 |
|:----------:|:-----------:|:------------|
|eventName|String|任意の名前を指定してください。特に指定がない場は、"_purchase" を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|orderID|String|（任意）注⽂番号等を指定します。|
|sku|String|（任意）商品コード等を指定します。|
|<span style="color:grey">itemName|<span style="color:grey">String|<span style="color:grey">使用しません。|
|price|double|商品総額を指定します。<br><span style="color:red">※必ず price * quantity の値が商品総額となるよう指定ください|
|quantity|int|1を指定してください。|
|currency|int|通貨コードを指定します。<br>nullの場合は”JPY”が指定されます。|
|eventInfo(fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|
|eventInfo(transaction.id)|JSONObject|注文番号、問い合わせ番号などのトランザクションID|
|eventInfo(product[].id)|JSONObject|商品IDデータフィードと同じ商品IDを使用してください。|
|eventInfo(product[].price)|JSONObject|該当商品の価格を設定します。|
|eventInfo(product[].quantity)|JSONObject|該当商品を買った個数を設定します。|
|eventInfo(din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo(criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)<br>以下、このアクションによってデータフィードが変動する場合に設定します。|
|eventInfo(datafeed.product)|JSONObject|変動するデータフィードを設定します。|
|eventInfo(datafeed.product[].id)|JSONObject|データフィードのアイテムを一意に識別するIDです。|
|eventInfo(datafeed.product[].action)|JSONObject|データフィードをどのように変更するかを入力します。<br>U:追加もしくは編集　D:削除|
|eventInfo(datafeed.product[].name)|JSONObject|アイテム名です。<br>以下全て、削除の際はnullで構いません。|
|eventInfo(datafeed.product[].expire)|JSONObject|アイテムの有効期限です。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。nullでも構いません。|
|eventInfo(datafeed.product[].effective)|JSONObject|アイテムの公開日時です。<br>これが設定された場合、公開日時になるまで商品はでません表示されません。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。nullでも構いません。|
|eventInfo(datafeed.product[].img)|JSONObject|アイテムの画像URLです。<br>nullでも構いません。|
|eventInfo(datafeed.product[].category1)|JSONObject|第一階層のカテゴリを指定します。<br>nullでも構いません。|
|eventInfo(datafeed.product[].category2)|JSONObject|第二階層のカテゴリを指定します。<br>nullでも構いません。|
|eventInfo(datafeed.product[].category3)|JSONObject|第三階層のカテゴリを指定します。<br>nullでも構いません。|
|eventInfo(datafeed.product[].price)|JSONObject|アイテムの価格を指定します。<br>nullでも構いません。|
|eventInfo(datafeed.product[].currency)|JSONObject|アイテムの通貨コードを指定します。<br>nullの場合、JPYが適用されます。|
|eventInfo(datafeed.product[].{任意})|JSONObject|その他配信、分析に使用する項目を指定します。|

> ※ 商品購⼊イベントの price に⼊⼒する⾦額は必ず、Json データに指定した商品の総額 (price * quantity)となるよう指定してください。指定されていない場合、集計が正しく⾏われません。
