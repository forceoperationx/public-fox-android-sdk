#	View Product（商品閱覽事件）安裝方法

　在View Product（單個商品閱覽）事件發生的地點、請按照下面的例子來安裝流量分析的事件計測功能。

### 安裝實例

```java
JSONObject eventInfo = new JSONObject("{" +
                                 "'fox_cvpoint':12345," +
                                 "'product':[{'id': '111'}]," +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_content", null, null, 0, eventInfo);
```

### 参数详细

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|調用源的Activity的Context|
|eventName|String|請指定"\_view\_content"|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">不使用。|
|eventInfo|JSONObject|事件資訊詳細 (參考下面)|

#### 事件資訊詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (fox_cvpoint)|JSONObject|設定F.O.X的成果地點ID。|
|eventInfo (product)|JSONArray|把Product作為KEY，用數組形式設定商品ID。|
|&nbsp;&nbsp;eventInfo (product[].id)|JSONObject|設定閱覽的商品ID。|
|eventInfo (din/dout)|JSONObject|如果希望指定日期請輸入（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo帳號ID在同一個APP裡不一樣的時候請設定。(任意)|
　　

---
[返回](/lang/zh-tw/doc/fox_engagement/README.md)<br>
[TOP](/lang/zh-tw/README.md)
