# View Basket（購物筐）実装方法

　在View Basket（商品購入預定一覽）事件發生的地點、請按照下面的例子來安裝流量分析的Event計測功能。

### 安裝例

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

### 參數詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|調用來源的Activity的Context|
|eventName|String|請指定"\_add\_to\_cart"。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">不使用。|
|eventInfo|JSONObject|事件信息詳細 (參考下面)|


#### 事件信息詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (currency)|JSONObject|貨幣<br>如果設定為Nil/Null、默認為“JPY”|
|eventInfo (fox_cvpoint)|JSONObject|設定F.O.X的成果地點ID。|
|eventInfo (product)|JSONArray|把Product作為KEY，用數組形式設定商品ID。|
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品ID<br>請使用和數據字段相同的商品ID。|
|&nbsp;&nbsp;eventInfo (product[].price)|JSONObject|設定該商品的價格。|
|&nbsp;&nbsp;eventInfo (product[].quantity)|JSONObject|設定購入該商品的個數。|
|eventInfo (din/dout)|JSONObject|如果希望指定日期請輸入。（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo帳號ID在同一個APP裡不一樣的時候請設定。(任意)|


---
[返回](/lang/zh-tw/doc/fox_engagement/README.md)<br>
[Top](/lang/zh-tw/README.md)
