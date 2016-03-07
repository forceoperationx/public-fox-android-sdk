# Track Transaction（商品購入イベント）実装方法

Track Transaction（商品購入）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

### 実装例

```java
JSONObject eventInfo = new JSONObject("{" +
                                      "'fox_cvpoint':'12345'," +
                                      "'transaction_id':'ABCDFE’," +
                                      "'product':[" +
                                                "{'id':'1234','price':550,'quantity':1}," +
                                                "{'id':'1235','price':550,'quantity':2}," +
                                                "{'id':'1236','price':550,'quantity':2}" +
                                                "]," +
                                      "'din':'2016-01-02'," +
                                      "'dout':'2016-01-05'," +
                                      "'criteo_partner_id':'XXXXX'" +
                                      "'datafeed' : {" +
                                                    "'version':'v1.0'," +
                                                    "'product':[" +
                                                               "{" +
                                                                "'id':'12345'" +
                                                                "'action':'U'" +
                                                                "'name':'icecreame'" +
                                                                "'expire':'2016-10-31'" +
                                                                "'effective':'2016-04-01'" +
                                                                "'img':'http://pngimg.com/upload/ice_cream_PNG5099.png'" +
                                                                "'category1':'food'" +
                                                                "'price':'2750'" +
                                                                "'currency':'JPY'"
                                                               "}"
                                                              "]" +
                                                    "}" +
                                      "}");
AnalyticsManager.sendEvent(this, "_purchase", null, null, null, null, null, 2750, 5, "JPY",　eventInfo);
```

### 引数詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|呼び出し元のActivityのContext|
|eventName|String|任意の名前を指定してください。特に指定がない場は、"_purchase" を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|orderID|String|（任意）注⽂番号等を指定します。|
|sku|String|（任意）商品コード等を指定します。|
|<span style="color:grey">itemName|<span style="color:grey">String|<span style="color:grey">使用しません。|
|price|double|商品総額を指定します。<br><span style="color:red">※必ず price * quantity の値が商品総額となるよう指定ください|
|quantity|int|1を指定してください。|
|currency|int|通貨コードを指定します。<br>nullの場合は”JPY”が指定されます。|
|eventInfo|JSONObject|イベント情報詳細 (以下参照)|

#### イベント情報詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|
|eventInfo (transaction.id)|JSONObject|注文番号、問い合わせ番号などのトランザクションID|
|eventInfo (product)|JSONArray|Product をキーとして商品IDを配列で設定します。
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品IDデータフィードと同じ商品IDを使用してください。|
|&nbsp;&nbsp;eventInfo (product[].price)|JSONObject|該当商品の価格を設定します。|
|&nbsp;&nbsp;eventInfo (product[].quantity)|JSONObject|該当商品を買った個数を設定します。|
|eventInfo (din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)<br>以下、このアクションによってデータフィードが変動する場合に設定します。|
|eventInfo (datafeed)|JSONObject|リアルタイムデータフィード (以下参照)|

#### データフィード詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|datafeed (version)|JSONObject|データフィードのバージョンを指定します。|
|datafeed (product)|JSONArray|変動するデータフィードを設定します。|
|&nbsp;&nbsp;datafeed (product[].id)|JSONObject|データフィードのアイテムを一意に識別するIDです。|
|&nbsp;&nbsp;datafeed (product[].action)|JSONObject|データフィードをどのように変更するかを入力します。<br>U:追加もしくは編集　D:削除|
|&nbsp;&nbsp;datafeed (product[].name)|JSONObject|アイテム名です。<br>以下全て、削除の際はnullで構いません。|
|&nbsp;&nbsp;datafeed (product[].expire)|JSONObject|アイテムの有効期限です。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].effective)|JSONObject|アイテムの公開日時です。<br>これが設定された場合、公開日時になるまで商品はでません表示されません。<br>「yyyy-MM-dd HH:mm:ss」もしくは「yyyy-MM-dd」の書式で日付を入力してください。nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].img)|JSONObject|アイテムの画像URLです。<br>nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].category1)|JSONObject|第一階層のカテゴリを指定します。<br>nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].category2)|JSONObject|第二階層のカテゴリを指定します。<br>nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].category3)|JSONObject|第三階層のカテゴリを指定します。<br>nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].price)|JSONObject|アイテムの価格を指定します。<br>nullでも構いません。|
|&nbsp;&nbsp;datafeed (product[].currency)|JSONObject|アイテムの通貨コードを指定します。<br>nullの場合、JPYが適用されます。|
|&nbsp;&nbsp;datafeed (product[].{任意})|JSONObject|その他配信、分析に使用する項目を指定します。|

> ※ 商品購⼊イベントの price に⼊⼒する⾦額は必ず、Json データに指定した商品の総額 (price * quantity)となるよう指定してください。指定されていない場合、集計が正しく⾏われません。


---
[戻る](/lang/ja/doc/fox_engagement/README.md)
[トップ](/lang/ja/README.md)
