## 有關利用Tag的LTV計測

在WEB頁面上進行會員登錄或商品購入的場合，可以利用img Tag進行LTV計測。

F.O.X的LTV計測適用於外部瀏覽器和APP內WebView。外部瀏覽器利用ltvOpenBrowser:而APP内WebView利用setLtvCookie來把LTV計測需要的資訊記錄到瀏覽器的Cookie裡。

## 使用外部瀏覽器的LTV計測

從APP啟動外部瀏覽器，使用外部瀏覽器表示WEB頁面進行Tag計測的場合，請利用ltvOpenBrowser:方法來啟動外部瀏覽器。用字符串形式設定外部瀏覽器使用的URL給參數。

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.ltvOpenBrowser("http://yourhost.com/");
```

## 使用APP內WebView的LTV計測

如果畫面遷移在APP內WebView裡進行，可以利用setLtvCookie。請在生成WebView的地方安裝下面的代碼。多次生成和廢棄WebView的場合，請在生成之際執行setLtvCookie。在內部利用android.webkit.CookieManagerとandroid.webkit.CookieSyncManager來設定Cookie。

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
WebView mWebView = (WebView) findViewById(R.id.sample_webview);
ltv.setLtvCookie(mWebView);

// ...
```
> ※從Android L開始，第三方Cookie默認設定為OFF。因此在setLtvCookie方法裡讓參數傳遞WebView，在方法內部使用CookieManager.setAccesptThirdPartyCookies來寫入第三方Cookie。

## Tag的安裝

請在LTV成果地點頁面裡安裝F.O.X管理員提供的計測Tag。

Tag的參數式樣如下。

|參數名|必須|備考|
|:-----|:-----|:-----|
|_buyer|必須|識別廣告主的ID。<br />請輸入管理員提供的值。。|
|_cvpoint|必須|識別廣告主的ID。<br />請輸入管理員提供的值。。|
|_price|オプション|付費額。付費計測時請設定。|
|_currency|オプション|半角字母數字3位長度的貨幣代碼。<br />付費計測時請設定。<br />如果未設定、_price默認設定為JPY(日圓)。|
|_buid|オプション|半角字母數字最大64位長度。<br />像會員ID這樣為每個用戶保持唯一值的時候請使用。|


在_currency裡用[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)定義的貨幣代碼來指定。

---
[TOP](/lang/zh-tw/README.md)
