[TOP](../../README.md)　>　イベント計測の詳細

---

# イベント計測の詳細

以下、各種イベントを実装する際の詳細を説明します。<br>
エンゲージメント計測やダイナミック配信連携を行う際に必要となる実装も含まれます。本実装を行うことで、媒体を横断したイベント計測連携が可能となります。

* **[1. アプリ内の各種イベント実装例](#each_event_sample)**
* **[2. 旧バージョンでの実装の置き換え(エンゲージメント計測)](#continuity)**
* **[3. タグを利用したイベント計測について](#track_by_tag)**

<div id="each_event_sample"></div>

## 1. アプリ内の各種イベント実装例

#### 　1.1 会員登録イベント計測 実装例

```java
// 会員登録イベント計測
Button registerBtn = (Button) findViewById(R.id.ltv_register);
registerBtn.setOnClickListener(new OnClickListener() {

  @Override
  public void onClick(View v) {
    FoxEvent event = new FoxEvent("_register_account", 5473);
    event.buid = "USER_A001";
    Fox.trackEvent(event);
  }
});
```

#### 　1.2 チュートリアル完了イベント計測 実装例

```java
// チュートリアル完了ベント計測
FoxEvent tutorialComp = new FoxEvent("_tutorial_comp", 456);
tutorialComp.buid = "USER_A001";
Fox.trackEvent(tutorialComp);
```

#### 　1.3 課金イベント計測 実装例

```java
// 課金イベント計測
FoxEvent purchaseEvent = new FoxEvent("_purchase", 123);
purchaseEvent.price = 1.2;
purchaseEvent.currency = "USD";
purchaseEvent.buid = "USER_A001";
purchaseEvent.sku = "ABC789";
purchaseEvent.quantity = 2;
Fox.trackEvent(purchaseEvent);
```

<div id="continuity"></div>

## 2. 旧バージョンでの実装の置き換え(エンゲージメント計測)

これまでのF.O.X Android SDK 3.0.0以下で行っていた実装方法を継続することも可能となっています。<br>
下記は課金イベントのエンゲージメント計測の実装例です。

**実装例**

```java
import org.json.JSONObject;
import co.cyberz.fox.FoxTrack;
import co.cyberz.fox.service.FoxEvent;

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
FoxEvent purchaseEvent = new FoxEvent("_purchase", 12345);
purchaseEvent.buid = "USER_A001";
purchaseEvent.price = 2750;
purchaseEvent.currency = "JPY";
purchaseEvent.quantity = 1;
purchaseEvent.sku = @"ABC789";
purchaseEvent.orderId = "ABCDFE";
purchaseEvent.eventInfo = eventInfo;
Fox.trackEvent(purchaseEvent);
```

> ※ 各種アプリ内イベントの媒体連携計測を行う場合、[`F.O.X Android SDK Extension`](https://github.com/cyber-z/fox-android-sdk-extension/blob/master/doc/lang/ja/README.md)を利用することで、実装するコード量を削減することが可能となります。

<div id="track_by_tag"></div>

## 3. タグを利用したイベント計測について

会員登録や商品購入等がWebページで行われる場合に、imgタグを利用してイベント計測を利用することができます。<br>
F.O.Xのイベント計測は、外部ブラウザ、アプリ内WebViewの両方に対応しています。外部ブラウザの場合には[`trackEventByBrowser`](../sdk_api/README.md#fox)メソッド、アプリ内WebViewの場合には[`trackEventByWebView`](../sdk_api/README.md#fox)メソッドを利用することで、F.O.Xがイベント計測に必要な情報をブラウザのCookieに記録します。

LTVの成果地点となるWebページに計測用HTMLタグを設置してください。計測用HTMLタグは弊社管理者より連絡致します。<br>
HTMLタグに利用するパラメータは以下の通りです。

|パラメータ名|必須|備考|
|:-----|:-----|:-----|
|_buyer|必須|広告主を識別するID。<br />管理者より連絡しますので、その値を入力してください。|
|_cvpoint|必須|成果地点を識別するID。<br />管理者より連絡しますので、その値を入力してください。|
|_price|オプション|課金額。課金計測時に設定してください。|
|_currency|オプション|半角英字3文字の通貨コード。<br />課金計測時に設定してください。<br />通貨が設定されていない場合、_priceをJPY(日本円)として扱います。|
|_buid|オプション|半角英数字64文字まで。<br />会員IDなどユーザー毎にユニークな値を保持する場合にご使用ください。|

> _currencyには[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)で定義された通貨コードを指定してください。


<div id="track_by_browser"></div>

### 3.1 外部ブラウザによるイベント計測

アプリケーションから外部ブラウザを起動し、外部ブラウザで表示したWebページでタグ計測を行う場合は、`trackEventByBrowser`メソッドを利用して外部ブラウザを起動してください。引数には、外部ブラウザで表示するURLを文字列で指定します。

```java
import co.cyberz.fox.Fox;

// ...
Fox.trackEventByBrowser("https://www.yoursite.com/tagpage");
```

<div id="track_by_webview"></div>

### 3.2 アプリ内WebViewでのイベント計測について

#### WebViewからネイティブAPIをコールする

WebViewが提供する機構を使い、JavaScript経由でネイティブAPIを実行します。

参考:https://developer.android.com/reference/android/webkit/WebView.html#addJavascriptInterface(java.lang.Object,%20java.lang.String)


ネイティブ側のサンプルコード

```java
import co.cyberz.fox.Fox;

public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState)
    WebView mWebView = (WebView) findViewById(R.id.sample_webview);
    mWebView.setWebViewClient(new WebViewClient());

    // WebViewからNativeCallでtrackEventを実行
    class NativeBridge {
        @JavascriptInterface
        public void sendFoxEvent(String event_name, int ltvId) {
            FoxEvent event = new FoxEvent(event_name, ltvId);
            Fox.trackEvent(event);
        }
    }
    mWebView.getSettings().setJavaScriptEnabled(true);
    mWebView.addJavascriptInterface(new NativeBridge(), "NativeBridge");
}
```

JavaScript側のサンプルコード

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
</head>
<body>
<script type="text/javascript">
  var event_name = 'foxEvent'
  var ltvId = 00000
  NativeBridge.sendFoxEvent(event_name, ltvId);
</script>
</body>
</html>
```


#### WevViewでタグ計測を行う

ユーザーの遷移がWebView内で行われる場合には、`trackEventByWebView`を用いることで計測することができます。WebViewが生成される箇所で下記コードを実行してください。WebViewが複数回生成・破棄される場合には、生成される度に`trackEventByWebView`が実行されるようにしてください。内部的にandroid.webkit.CookieManagerとandroid.webkit.CookieSyncManagerを利用してCookieをセットします。

```java
import co.cyberz.fox.Fox;

// ...
@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  WebView mWebView = (WebView) findViewById(R.id.sample_webview);
	Fox.trackEventByWebView(mWebView);
	mWebView.loadUrl("http://www.mysite.com/event/");
}
```

> ※ Android LよりサードパーティCookieがデフォルトでOFFとなります。 そのためtrackEventByWebViewメソッドでは引数にWebViewを持たせることで、内部ではCookieManager.setAccesptThirdPartyCookiesを用いてサードパーティCookieの書込みを行っております。



---
[トップ](../../README.md)
