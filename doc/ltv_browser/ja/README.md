## タグを利用したLTV計測について

会員登録や商品購入等がWebページで行われる場合に、imgタグを利用してLTV計測を利用することができます。

F.O.XのLTV計測は、外部ブラウザ、アプリ内WebViewの両方に対応しています。外部ブラウザの場合にはltvOpenBrowser、アプリ内WebViewの場合にはsetLtvCookieメソッドを利用することで、F.O.XがLTV計測に必要な情報をブラウザのCookieに記録します。

### 外部ブラウザでのLTV計測

アプリケーションから外部ブラウザを起動し、外部ブラウザで表示したWebページでタグ計測を行う場合は、ltvOpenBrowserメソッドを利用して外部ブラウザを起動してください。引数には、外部ブラウザで表示するURLを文字列で指定します。

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.ltvOpenBrowser("http://yourhost.com/");
```

### アプリ内WebViewでのLTV計測

ユーザーの遷移がWebView内で行われる場合には、setLtvCookieを利用することができます。WebViewが生成される箇所で下記コードを実行してください。WebViewが複数回生成・破棄される場合には、生成される度にsetLtvCookieが実行されるようにしてください。内部的にandroid.webkit.CookieManagerとandroid.webkit.CookieSybcManagerを利用してCookieをセットします。

```java
import jp.appAdForce.android.LtvManager;

// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.setLtvCookie();

// setLtvCookieを実行した後に、WebViewの表示を行います。
// ...
```

### タグの実装

LTVの成果地点となるページに計測タグを実装してください。計測タグはForce Operation X管理者から連絡いたします。

タグに利用するパラメータ仕様は以下の通りです。

|パラメータ名|必須|備考|
|:-----|:-----|:-----|
|_buyer|必須|広告主を識別するID。<br />管理者より連絡しますので、その値を入力してください。|
|_cvpoint|必須|成果地点を識別するID。<br />管理者より連絡しますので、その値を入力してください。|
|_price|オプション|課金額。課金計測時に設定してください。|
|_currency|オプション|半角英字3文字の通貨コード。<br />課金計測時に設定してください。<br />通貨が設定されていない場合、_priceをJPY(日本円)として扱います。|
|_buid|オプション|半角英数字64文字まで。<br />会員IDなどユーザー毎にユニークな値を保持する場合にご使用ください。|


_currencyには[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)で定義された通貨コードを指定してください。


