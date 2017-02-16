## LTV tracking by using tag

LTV tracking can be performed using &lt;img&gt; tag when member registration, product purchase, etc. are done on a web page.

F.O.X's LTV measurement supports both browsers and in-app WebViews. F.O.X records necessary information for LTV tracking in the browser's cookie.

### LTV tracking by using browser.

By using the `ltvOpenBrowser` method to start up the browser, tag measurement can be performed. Specify the URL displayed in the external browser as a string for the argument of the `ltvOpenBrowser` method.

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.ltvOpenBrowser("http://yourhost.com/");
```

### LTV tracking by using In-app WebView

In case of tag tracking by displaying not browser but WebView in app, add below method to pass Cookie information to WebView. In case of LTV tracking by WebView, Cookie is deleted by closing WebView. Launching browser several times, code has to be located in each part.

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
WebView mWebView = (WebView) findViewById(R.id.sample_webview);
ltv.setLtvCookie(mWebView);

// ...
```
> Third party cookies are turned OFF by default from Android L. Therefore, by setting the argument to WebView in the setLtvCookie method, F.O.X SDK internally use CookieManager.setAccesptThirdPartyCookies to write the third party cookie.

### Put the tracking tag

Please put the tracking tag on the page which is the result point of LTV. The tracking tag will be contacted by the F.O.X operator.

Detail for each parameter in tag is below

|Parameter|Mandatory|Note|
|:-----|:-----|:-----|
|_buyer|Mandatory|ID to identify advertiser.<br />Operators give them and set those values.|
|_cvpoint|Required|ID to identify cv point.<br />Operators give them and set those values.|
|_price|Optional|For managing amount of sales.|
|_currency|Optional|Set the currency code In the case of null, "JPY" is specified.|
|_buid|Optional|For managing unique ID such as member ID.<br /> ※Within 64 characters and half-width, alphanumeric characters only.|


Specify the currency code defined by [ISO 4217](http://en.wikipedia.org/wiki/ISO_4217) for currency.

---
[TOP](/lang/en/README.md)
