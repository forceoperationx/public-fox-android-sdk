#	View Listing（多個商品閱覽事件）安裝方法

　在View Listing（檢索結果・一覽畫面）事件發生的地點、請按照下面的例子來安裝流量分析的Event計測功能。先頭的3個商品被計測。

### 安裝實例

```java
JSONObject eventInfo = new JSONObject("{" +
                                 "'fox_cvpoint': '12345'," +
                                 "'product':[" +
                                        "{'id': '111'," +
                                        "'category':'電影、錄像>DVD>體育、休閑'}," +
                                        "{'id': '112'," +
                                         "'category':'電影、錄像>DVD>體育、休閑'}," +
                                        "{'id': '113'," +
                                         "'category':'電影、錄像>DVD>體育、休閑'}" +
                                 "]," +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_listing", null, null, 0, eventInfo);
```

### 參數詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|調用源的Activity的Context|
|eventName|String|請指定"\_view\_listing"|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">不使用。|
|eventInfo|JSONObject|事件信息詳細 (參考下面)|

#### 事件信息詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (product)|JSONArray|把Product作為KEY，用數組形式設定商品ID。|
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|商品ID<br>請使用和數據字段相同的商品ID。|
|&nbsp;&nbsp;eventInfo (product[].category)|JSONObject|設定商品種別。<br>請使用和數據字段相同的商品種別。<br>如果一個商品有多個種別請用「,」區分、分層次請用「>」來分割。<br>例）電影，錄像>DVD>體育，休閑，可以設定成null。|
|eventInfo (din/dout)|JSONObject|如果希望指定日期請輸入（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo帳號ID在同一個APP裡不一樣的時候請設定。(任意)|
|eventInfo (fox_cvpoint)|JSONObject|設定F.O.X的成果地點ID。|


---
[返回](/lang/zh-tw/doc/fox_engagement/README.md)
[Top](/lang/zh-tw/README.md)
