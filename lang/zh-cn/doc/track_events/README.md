[TOP](../../README.md)　>　イベント計測の詳細

---

# 事件计测的详细

以下为实际安装各类事件时的详细说明。<br>
包含一般广告计测及动态投放连动时所需的执行任务。通过本章的安装作业可以进行跨媒体事件监测。

* **[1. APP内各类事件的安装案例](#each_event_sample)**
* **[2. 替换旧版里的处理代码（一般广告计测）](#continuity)**
* **[3. 使用Tag进行事件计测](#track_by_tag)**

<div id="each_event_sample"></div>
## 1. APP内各类事件的安装案例

#### 　1.1 会员登录事件的安装案例

```java
// 会员登录事件计测
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

#### 　1.2 完成新手引导事件计测的安装实例

```java
// 完成新手引导事件计测
FoxEvent tutorialComp = new FoxEvent("_tutorial_comp", 456);
tutorialComp.buid = "USER_A001";
Fox.trackEvent(tutorialComp);
```

#### 　1.3 付费事件计测的安装实例

```java
// 付费事件计测
FoxEvent purchaseEvent = new FoxEvent("_purchase", 123);
purchaseEvent.price = 1.2;
purchaseEvent.currency = "USD";
purchaseEvent.buid = "USER_A001";
purchaseEvent.sku = "ABC789";
purchaseEvent.quantity = 2;
Fox.trackEvent(purchaseEvent);
```

<div id="continuity"></div>
## 2. 替换旧版里的处理代码（一般广告计测）

これまでのF.O.X Android SDK 3.0.0以下で行っていた実装方法を継続することも可能となっています。<br>
下記は課金イベントのエンゲージメント計測の実装例です。
可沿用F.O.X Android SDK 3.0.0及之前版本的执行方式。<br>
以下为付费计测的执行说明。

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

> ※ 对各类APP内事件的媒体连动计测时，通过使用[`F.O.X Android SDK Extension`](https://github.com/cyber-z/fox-android-sdk-extension/blob/master/doc/lang/ja/README.md)可以减少执行代码量。

<div id="track_by_tag"></div>
## 3. 使用Tag进行事件计测

在网页上发生会员登录及商品购买等行为时，可以使用img tag来进行事件测量。
F.O.X的事件计测适用于外部浏览器和APP内WebView。外部浏览器使用[`trackEventByBrowser`](../sdk_api/README.md#fox)方法，APP内WebView使用[`trackEventByWebView`](../sdk_api/README.md#fox)方法，F.O.X会在页面Cookie中记录事件计测所需的信息。

<div id="track_by_browser"></div>
### 3.1 使用外部浏览器进行事件计测

从APP跳转至外部浏览器，利用跳转的网页进行tag计测时，请使用`trackEventByBrowser`方法来启动外部浏览器。并在参数中用字符串指定要跳转的URL。

```java
import co.cyberz.fox.Fox;

// ...
Fox.trackEventByBrowser("https://www.yoursite.com/tagpage");
```

<div id="track_by_webview"></div>
### 3.2 APP内WebView的事件计测

在WebView内进行用户跳转的情况时，可以使用`trackEventByWebView`来进行测量。请在加载WebView的位置执行下列代码。WebView多次加载・加载错误时，请务必在每次加载时执行`trackEventByWebView`。内部将会通过android.webkit.CookieManager和android.webkit.CookieSyncManager来设置Cookie。

```java
import co.cyberz.fox.Fox;

// ...
WebView webView = findViewById(R.id.sample_webview);
Fox.trackEventByWebView(webView);
webView.loadUrl("https://www.yoursite.com/tagpage");
```

> ※ Android L中第三方Cookie默认为OFF。 因此通过trackEventByWebView方法，在参数中设置WebView，内部便可以用CookieManager.setAccesptThirdPartyCookies来写入第三方Cookie了。

---
[Top](../../README.md)
