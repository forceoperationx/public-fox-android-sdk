# View Basket（買い物かご）実装方法

　View Basket（商品購入予定一覧）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

### 実装例

```java
JSONObject eventInfo = new JSONObject("{" +
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
AnalyticsManager.sendEvent(this, "_add_to_cart", null, null, 0, eventInfo);
```

### 引数詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|呼び出し元のActivityのContext|
|eventName|String|"\_add\_to\_cart" を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo|JSONObject|イベント情報詳細 (以下参照)|


#### イベント情報詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (currency)|JSONObject|通貨<br>Nil/Nullの場合、デフォルト “JPY”|
|eventInfo (fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|
|eventInfo (product)|JSONArray|Product をキーとして商品IDを配列で設定します。
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品ID<br>データフィードと同じ商品IDを使用してください。|
|&nbsp;&nbsp;eventInfo (product[].price)|JSONObject|該当商品の価格を設定します。|
|&nbsp;&nbsp;eventInfo (product[].quantity)|JSONObject|該当商品を買った個数を設定します。|
|eventInfo (din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)|


---
[戻る](/lang/ja/doc/fox_engagement/README.md)
[トップ](/lang/ja/README.md)
