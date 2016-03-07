#	View Home(アプリトップ訪問イベント)実装方法
　View Home（ホーム画面）イベントが発生する箇所に、下記に従ってアクセス解析のイベント計測機能を実装ください。

### 実装例

```java
JSONObject eventInfo = new JSONObject("{" +
                                 "'din':'2016-01-02'," +
                                 "'dout':'2016-01-05'," +
                                 "'criteo_partner_id':'XXXXX'," +
                                 "'fox_cvpoint': '12345'" +
                                 "}");
AnalyticsManager.sendEvent(this, "_view_toppage", null, null, 0, eventInfo);
```

### 引数詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|context|Context|呼び出し元のActivityのContext|
|eventName|String|“\_view\_toppage”を指定してください。|
|<span style="color:grey">action|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">label|<span style="color:grey">String|<span style="color:grey">使用しません。|
|<span style="color:grey">value|<span style="color:grey">int|<span style="color:grey">使用しません。|
|eventInfo|JSONObject|イベント情報詳細 (以下参照)|


#### イベント情報詳細

| 引数 | 型 | 概要 |
|:----------|:-----------:|:------------|
|eventInfo (din/dout)|JSONObject|日付の指定がある場合は入力（任意）|
|eventInfo (criteo_partner_id)|JSONObject|CriteoアカウントIDが同一アプリで異なる場合は入力(任意)|
|eventInfo (fox_cvpoint)|JSONObject|F.O.Xの成果地点IDを設定します。(任意)|


---
[戻る](/lang/ja/doc/fox_engagement/README.md)
[トップ](/lang/ja/README.md)
