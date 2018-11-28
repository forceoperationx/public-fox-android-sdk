[TOP](../../README.md)　>　Event measurement details

---

# Event measurement details

These are the instructions for implementing additional details. <br>
Implementation instructions for engagement measurement and dynamic transmission linkage are included. By implementing the following, your app will be able to attribute events to specific individual digital mediums.

* **[1. Examples of each app event](#each_event_sample)**
* **[2. Changing Implementation from a previous version(Engagement measurement)](#continuity)**
* **[3. Tracking events by tag](#track_by_tag)**

<div id="each_event_sample"></div>

## 1. Examples of each app event

#### 　1.1 Member registration tracking example

```java
// member registration event tracking
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

#### 　1.2 Tutorial completion event tracking example

```java
// Tutorial completion event tracking
FoxEvent tutorialComp = new FoxEvent("_tutorial_comp", 456);
tutorialComp.buid = "USER_A001";
Fox.trackEvent(tutorialComp);
```

#### 　1.3 In-App Purchase Event Tracking example

```java
// In-App purchase event tracking
FoxEvent purchaseEvent = new FoxEvent("_purchase", 123);
purchaseEvent.price = 1.2;
purchaseEvent.currency = "USD";
purchaseEvent.buid = "USER_A001";
purchaseEvent.sku = "ABC789";
purchaseEvent.quantity = 2;
Fox.trackEvent(purchaseEvent);
```

<div id="continuity"></div>

## 2. Changing Implementation from a previous version(Engagement measurement)

It is possible to continue using the same methods for engagement measurement as versions before 4.0.0.<br>
See the In-App purchase event engagement measurement implementation below.

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

<div id="track_by_tag"></div>

## 3. Tracking events by tag

When membership registration, product purchase, etc. are completed on a web page, event measurement can be performed using an `img` tag.<br>
There are two ways for doing this.
For external browsers, see the [`trackEventByBrowser`](../sdk_api/README.md#fox) method.
For web views, see the [`trackEventByWebView`](../sdk_api/README.md#fox) method.
By using either of these two methods, F.O.X can insert the data it needs in the cookies of the browser.

F.O.X will provide an html tag for you. Please insert this tag in the web page that you will use to measure LTV. <br>
Find the HTML tag parameters below.

|Parameter Name|Necessity|Comments|
|:-----|:-----|:-----|
|_buyer|necessary|This is your Advertiser ID.<br />F.O.X will provide you with the value to enter here.|
|_cvpoint|necessary|This is a conversion point ID<br/>F.O.X will provide you with the value to enter here.|
|_price|optional|This is the purchase price.<br> Please set this during transactions.|
|_currency|optional|Enter a 3 character alphanumeric currency code.<br>Please set this during transactions.<br />If this value is not set、the default currency for _price will be in JPY(yen).|
|_buid|optional|Up to 64 alphanumeric characters.<br />Use this parameter when you want to attach a unique ID to each individual member.|

> Please specify the currency code for `_currency` as defined in [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)


<div id="track_by_browser"></div>

### 3.1 Event Measurement by external browser

When using tags to track events in an external browser launched by your app, use the `trackEventByBrowser` method to launch the external browser.<br>
The arguments will be composed of strings from the external browser's URL.

```java
import co.cyberz.fox.Fox;

// ...
Fox.trackEventByBrowser("https://www.yoursite.com/tagpage");
```

<div id="track_by_webview"></div>

### 3.2 Event Measurement by web view

When managing user screen transitions with a web view, use the `trackEventByWebView` method to track events. Run the following code when initializing the web view.
Make sure that `trackEventByWebView` is run every time a web view is created or destroyed. Cookies will be set using android.webkit.CookieManager and android.webkit.CookieSyncManager internally.

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
> ※ Third party cookies are turned off by default starting from Android L. Therefore, by having the WebView as an argument in the trackEventByWebView method, we use CookieManager.setAccesptThirdPartyCookies internally to write third party cookies.


---
[Return to Top](../../README.md)
