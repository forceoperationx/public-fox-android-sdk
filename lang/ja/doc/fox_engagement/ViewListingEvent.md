#	View Listing（複数商品閲覧イベント）実装方法

　View Listing（検索結果・一覧画面）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。上位３つの商品が計測されます。

### 実装例

```java
JSONObject eventInfo = new JSONObject("{" +
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
AnalyticsManager.sendEvent(this, "_view_listing", null, null, 0, eventInfo);
```

### 引数詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|呼び出し元のActivityのContext|
|eventName|String|"\_view\_listing"を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo|JSONObject|イベント情報詳細 (以下参照)|

#### イベント情報詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (product)|JSONArray|Product をキーとして商品IDを配列で設定します。
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品IDを設定します。<br>データフィードと同じ商品IDを使⽤してください。|
|&nbsp;&nbsp;eventInfo (product[].category)|JSONObject|商品カテゴリを設定します。<br>データフィードと同じ商品カテゴリを使用してください。<br>１商品に対して複数カテゴリある場合はカンマ「,」区切り、階層がある場合は「>」で分割します。<br>例）映画、ビデオ>DVD>スポーツ、レジャーnullでも構いません。|
|eventInfo (din/dout)|JSONObject|⽇付の指定がある場合は⼊⼒してください。（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo アカウントID が同⼀アプリで異なる場合は⼊⼒(任意)|
|eventInfo (fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。|


---
[戻る](/lang/ja/doc/fox_engagement/README.md)
[トップ](/lang/ja/README.md)
