#	View Home(APP TOP訪問事件)安裝方法
　在View Home（HOME畫面）事件發生的地點、請按照下面的例子來安裝流量分析的事件計測功能。

### 安裝實例

```java
JSONObject eventInfo = new JSONObject("{" +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'," +
                                 "'fox_cvpoint': '12345'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_toppage", null, null, 0, eventInfo);
```

### 参数详细

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|調用源的Activity的Context|
|eventName|String|請指定“\_view\_toppage”|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">不使用。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">不使用。|
|eventInfo|JSONObject|事件資訊詳細 (參考下面)|


#### 事件資訊詳細

| 參數 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (din/dout)|JSONObject|如果希望指定日期請輸入（任意）|
|eventInfo (criteo_partner_id)|JSONObject|Criteo帳號ID在同一個APP裡不一樣的時候請設定。(任意)|
|eventInfo (fox_cvpoint)|JSONObject|設定F.O.X的成果地點ID。|


---
[返回](3.x/lang/zh-tw/doc/fox_engagement/README.md)<br>
[TOP](/3.x/lang/zh-tw/README.md)
