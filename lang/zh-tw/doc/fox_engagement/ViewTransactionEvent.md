# Track Transaction（商品購入事件）実装方法

在Track Transaction（商品購入）事件發生的地點、請按照下面的例子來安裝流量分析的Event計測功能。

### 安裝實例

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

### 参数详细

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|調用源的Activity的Context|
|eventName|String|請指定為任意名稱。如果沒有特別指定，請指定為"_purchase"。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">不使用。|
|orderID|String|（任意）指定訂單號。|
|sku|String|（任意）指定商品代號sku。|
|<span style="color:grey">itemName|<span style="color:grey">String|<span style="color:grey">不使用。|
|price|double|指定商品總額。<br><span style="color:red">※請務必把price * quantity的結果作為商品總額來指定|
|quantity|int|請指定為1。|
|currency|int|指定貨幣代碼。<br>null的場合默認指定為"JPY"。|
|eventInfo|JSONObject|事件信息詳細 (參考下面)|

#### 事件信息詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (fox_cvpoint)|JSONObject|設定F.O.X的成果地點ID。|
|eventInfo (transaction.id)|JSONObject|訂單號，諮詢號等處理事務ID|
|eventInfo (product)|JSONArray|把Product作為KEY，用數組形式設定商品ID。|
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品ID<br>請使用和數據字段相同的商品ID。|
|&nbsp;&nbsp;eventInfo (product[].price)|JSONObject|設定該商品的價格。|
|&nbsp;&nbsp;eventInfo (product[].quantity)|JSONObject|設定購入該商品的個數。|
|eventInfo (din/dout)|JSONObject|如果希望指定日期請輸入。（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo帳號ID在同一個APP裡不一樣的時候請設定。(任意)<br>在下方，根據這個Action的不同，在數據字段變動時做設定|
|eventInfo (datafeed)|JSONObject|實時數據字段 (參考下方)|

#### 數據字段詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|datafeed (version)|JSONObject|指定數據字段的版本。|
|datafeed (product)|JSONArray|指定變動的數據字段。|
|&nbsp;&nbsp;datafeed (product[].id)|JSONObject|能夠專門識別數據字段的商品的ID。|
|&nbsp;&nbsp;datafeed (product[].action)|JSONObject|輸入對數據字段的操作。<br>U:添加或編輯　D:刪除|
|&nbsp;&nbsp;datafeed (product[].name)|JSONObject|商品名。<br>下面的項目同樣，刪除時可以設定為null。|
|&nbsp;&nbsp;datafeed (product[].expire)|JSONObject|商品的有效期限。<br>請按照「yyyy-MM-dd HH:mm:ss」或者「yyyy-MM-dd」的格式來輸入日期。可以為null。|
|&nbsp;&nbsp;datafeed (product[].effective)|JSONObject|商品的公開日期和時間。<br>如果此項被設定，到公開日期和時間為止，商品不會被顯示出來。<br>請按照「yyyy-MM-dd HH:mm:ss」或「yyyy-MM-dd」的格式來輸入日期。可以為null。|
|&nbsp;&nbsp;datafeed (product[].img)|JSONObject|商品的畫像URL。<br>可以為null。|
|&nbsp;&nbsp;datafeed (product[].category1)|JSONObject|指定第一層次的種別。<br>可以為null。|
|&nbsp;&nbsp;datafeed (product[].category2)|JSONObject|指定第二層次的種別。<br>可以為null。|
|&nbsp;&nbsp;datafeed (product[].category3)|JSONObject|指定第三層次的種別。<br>可以為null。|
|&nbsp;&nbsp;datafeed (product[].price)|JSONObject|指定商品價格<br>可以為null。|
|&nbsp;&nbsp;datafeed (product[].currency)|JSONObject|指定商品的貨幣代碼。<br>null的場合默認指定為"JPY"。|
|&nbsp;&nbsp;datafeed (product[].{任意})|JSONObject|指定其他投放或分析使用的項目。|

> ※ 商品購入事件的price裡輸入的金額，請一定按照Json數據裡指定的商品總額(price * quantity)來指定。沒有指定的話，無法正常統計。


---
[返回](/lang/zh-tw/doc/fox_engagement/README.md)<br>
[Top](/lang/zh-tw/README.md)
