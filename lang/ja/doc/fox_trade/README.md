# 広告配信機能

## 1. 環境設定

組み込み対象のアプリにはGooglePlayServicesをご導入の上、AdvertisingIDを取得出来ることが必須となっております。<br>
AdvertisingIDを取得するには[`こちら`](/lang/ja/doc/google_play_services/README.md)をご確認ください。

## 1.1 AndroidManifest.xmlの設定

**[Activityの追加]**

インタースティシャル広告を表示する際に必須となるActivityとなります。<br>
以下、そのままコピーして&lt;application&gt;タグ内にご設定ください。

```xml
<activity
    android:name="co.cyberz.dahlia.DahliaActivity"
    android:theme="@android:style/Theme.Translucent" />
```

## 2. API

### BannerView

|返り値型|メソッド|詳細|
|---:|:---|:---|
|-|BannerView ( Context c )|コンストラクター|
|void|show ( String placementId )<br><br>`placementID` : 広告表示ID (管理者より発行されます)|バナー広告を表示します。|
|void|show ( String placementId, DahliaAdViewListener listener )<br><br>`placementID` : 広告表示ID (管理者より発行されます)<br>`listener` : 広告表示の際のイベントを取得するためのリスナー|バナー広告を表示します。|

### Interstitial

|返り値型|メソッド|詳細|
|---:|:---|:---|
|-|Interstitial ( Context c )|コンストラクター|
|void|show ( String placementId )<br><br>`placementID` : 広告表示ID (管理者より発行されます)|インタースティシャル広告を表示します。|
|void|show ( String placementId, DalInterStitialListener listener )<br><br>`placementID` : 広告表示ID (管理者より発行されます)<br>`listener` : 広告表示の際のイベントを取得するためのリスナー|インタースティシャル広告を表示します。|

### BannerView.OnStateListener

|返り値型|メソッド|詳細|
|---:|:---|:---|
|void|onSuccess ( View v )<br><br>`v` : 広告のView|バナー広告の表示が正常だった場合に呼ばれます。|
|void|onFailed ( View v ) <br><br> `v` : 広告のView|バナー広告が表示できなかった場合に呼ばれます。|

### Interstitial.OnStateListener

|返り値型|メソッド|詳細|
|---:|:---|:---|
|void|onSuccess ( )|インタースティシャル広告の表示が正常だった場合に呼ばれます。|
|void|onFailed ( )|インタースティシャル広告が表示できなかった場合に呼ばれます。|
|void|onClosed ( )|インタースティシャル広告が閉じられた際にに呼ばれます。|

> インタースティシャル広告が表示できなかった場合、`onFailed`メソッドが呼ばれた後にも`onClosed`メソッドは呼ばれます。

### AdOperation

広告表示をカスタマイズする際に使用するAPI

|返り値型|メソッド|詳細|
|---:|:---|:---|
|-|AdOperation ( Context c )|コンストラクター|
|void|requestAdInfo ( String placementId, AdOperationListener listener )<br><br>`placementID` : 広告表示ID (管理者より発行されます)<br>listener : 広告表示に必要なJSONを取得するためのコールバック|広告情報要求API。広告表示に必要なJSONオブジェクトをAdOperationListenerによって返します|
|void|sendImp ( Context c, String placementId, String sessionId, boolean impStatus )<br><br>`placementID` : 広告表示ID (管理者より発行されます)、requestAdInfoで指定したものと同じIDを指定します。<br>`sessionId` : JSONオブジェクトに含まれるセッションID<br>`impStatus` : 広告表示の正否をbooleanで指定|requestAdInfoで取得した広告が表示ができたかを送信するためのAPI。JSONに含まれている`session`を指定する必要があります。|
|void|sendClick ( Context c, String placementId, String sessionId )<br><br>`placementID` : 広告表示ID (管理者より発行されます)、requestAdInfoで指定したものと同じIDを指定します。<br>`sessionId` : JSONオブジェクトに含まれるセッションID|requestAdInfoで取得した広告がタップされたことを送信するためのAPI。JSONに含まれている`session`を指定する必要があります。|

### AdOperation.AdOperationListener

広告情報のリクエストAPIのコールバック

|返り値型|メソッド|詳細|
|---:|:---|:---|
|void|onReceiveAdInfo ( JSONObject receivedObject )<br><br>`receivedObject` : 広告表示に必要な情報が格納されたJSONObject|リクエストが正常だった場合に呼ばれます。引数には広告情報が格納されたJSONObjectが返ってきます。|
|void|onRequestFailed ( )|リクエストが失敗した場合に呼ばれます。|

## 3. コードへの組み込み

### 3.1 バナー広告表示サンプル その１

javaコードのみでの実装

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.test_activity);

   // 既存レイアウトに追加
   LinearLayout ll = (LinearLayout) findViewById(R.id.banner_layout);
   // バナー広告表示View
   BannerView mBannerView = new BannerView(this);
   mBannerView.show("広告表示ID", new BannerView.OnStateListener() {
      @Override
      public void onSuccess(View v) {
        // バナー広告が正常に表示された場合の処理
        Toast.makeText(this, "成功", Toast.LENGTH_SHORT).show();
      }

      @Override
      public void onFailed(View v) {
        // バナー広告の表示に失敗した場合の処理
        Toast.makeText(this, "失敗", Toast.LENGTH_SHORT).show();
        v.setVisibility(View.GONE);
      }
   });
   ll.addView(mBannerView);
}
```

### 3.2 バナー広告表示サンプル その２

layoutのXMLで定義しての実装

[xml]
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <co.cyberz.dahlia.BannerView
        android:id="@+id/banner"
        android:layout_gravity="center_horizontal"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>  
```
> `BannerView`は管理画面に登録したクリエイティブのサイズに自動調整します。横幅と縦幅の指定には`wrap_content`を指定ください。

[java]
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.test_activity);

   // xmlからBannerViewオブジェクトの呼び出し
   BannerView mBannerView = (BannerView) findViewById(R.id.banner);
   // バナー広告表示View
   mBannerView.show("広告表示ID");
}
```

### 3.3 インタースティシャル広告表示サンプル その１

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.test_activity);
    // インタースティシャル表示用メソッド
    Interstitial mInterstitial = new Interstitial(this);
    mInterstitial.show("広告表示ID");
}
```

### 3.4 インタースティシャル広告表示サンプル その２

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.test_activity);
    // インタースティシャル表示用メソッド
    Interstitial mInterstitial = new Interstitial(this);
    mInterstitial.show("広告表示ID", new Interstitial.OnStateListener {
      @Override
      public void onSuccess() {
        // インタースティシャル広告が正常に表示された場合の処理
        Toast.makeText(this, "成功", Toast.LENGTH_SHORT).show();
      }

      @Override
      public void onFailed() {
        // インタースティシャル広告の表示に失敗した場合の処理
        Toast.makeText(this, "失敗", Toast.LENGTH_SHORT).show();
      }

      @Override
      public void onClosed() {
        // インタースティシャル広告を閉じられた場合の処理

      }
    });
}
```

> 使用例 : 画面A→画面Bへ遷移する際、インタースティシャル広告を表示してから遷移させるなど<br>
（画面A→インタースティシャル広告→画面B）<br>その場合、画面Aにて上記実装を行い、`onSuccess`,`onFailed`,`onClose`の各々のメソッド内に画面Bへの遷移する処理を実装することで可能となります。


### 3.5 広告をカスタマイズ表示する際の実装サンプル

```java
class TestActivity extends Activity implements View.OnClickListener {

  private JSONObject adInfo;
  private final String placementId = "広告表示ID";

  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.test_activity);

      //広告情報のリクエスト
      AdOperation ado = new AdOperation(this);
      ado.requestAdInfo(placementId, new AdOperation.AdOperationListener() {
        @Override
        public void onReceiveAdInfo(JSONObject receivedObject) {
          adInfo = receivedObject;
          try {
            // 広告クリエイティブURL
            String adCreativeUrl = receivedObject.getString("curl");
            // 画像のInputStream取得(下記の取得メソッドは例)
            InputStream iStream = getImageInputStream(adCreativeUrl);
            Bitmap bitmap = BitmapFactory.decodeStream(iStream);
            ImageView adImage = TestActivity.this.findViewById(R.id.ad_image);
            // 広告のクリックリスナー
            adImage.setOnClickListener(this);
            // 広告クリエイティブを表示
            adImage.setImageBitmap(bitmap);
            // 取得した広告情報のセッションID
            String sessionId = receivedObject.getString("session");
            AdOperation.sendImp(TestActivity.this, placementId, sessionId, true);
          } catch (JSONException e) {
            e.printStackTrace();
          }
        }

        @Override
        public void onRequestFailed() {
            // 取得に失敗
            ImageView adImage = TestActivity.this.findViewById(R.id.ad_image);
            adImage.setVisibility(View.GONE);
        }
      });
  }

  @Override
  public void onClick(View v) {
    // 広告がクリックされた場合
    String sessionId = adInfo.getString("session");
    AdOperation.sendClick(TestActivity.this, placementId, sessionId);
  }
}
```

## 4. 表示サンプル

<table>
<tr>
<td align="center" style="border-style:none;">[バナー広告サンプル]</td>
<td align="center" style="border-style:none;">[インタースティシャル広告サンプル]</td>
</tr>
<tr>
<td style="border-style:none;"><img src="./sample_banner.png" width="300px"/></td>
<td style="border-style:none;"><img src="./sample_interstitial.png" width="300px"/></td>
</tr>
</table>

---
[トップ](/lang/ja/README.md)
