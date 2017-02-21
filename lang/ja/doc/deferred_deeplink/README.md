[TOP](../../README.md)　>　ディファードディープリンクの実装

---

# ディファードディープリンクの実装

ディファードディープリンクを実装することで、広告をクリックした際に紐付いているディープリンクにリダイレクトし
アプリ内の対象のページに遷移することが可能となります。また、アプリが未インストールの場合でも、インストール後に
広告のリダイレクト先となるディープリンクに遷移させることが可能となります。

> サポートバージョン : 4.1.0以上

以下、ディファードディープリンクの実装に必要なクラス及び、メソッド情報のみを説明しています。

## FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|返り値|メソッド|実装箇所|説明|
|:---:|:---|:---:|:---|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)|onCreate|本メソッドをコールすることでインストール計測の際に、サーバへディファードディープリンクを問い合わせる処理が実行されます。|

<div id="deeplinkhandler"></di>
## DeeplinkHandler

public static abstract class DeeplinkHandler

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption$DeeplinkHandler

本クラスはFoxTrackOptionクラスのインナークラスとなります。

|返り値|タイプ|メソッド|実装箇所|説明|
|:---:|:---:|:---|:---:|:---|
|void|abstract|**onReceived** (JSONObject jObject)|onCreate|ディファードディープリンクを受信した際に受け取るためのメソッド。ディファードディープリンクが存在しない場合や、端末が通信に失敗する場合にはnullを返します。|
|long|-|**getDuration** ()|DeeplinkHandler拡張クラス内|[オプション]ディファードディープリンクをサーバに問い合わせる際に、対象となるラストクリックの対象期間(秒)（どれだけ遡るかを秒数で指定）<br>デフォルト : 86400 (1日)|

### 受信するJSONObjectの形式

上記、DeeplinkHandlerのonReceivedメソッドの引数で受け取るJsonの形式は以下の通りです。

```json
{
  "deeplink":"targetapp://details?id=12345&category=1"
}
```

## 注意事項

* **Cookie計測の場合**<br><br>
Cookie計測においてブラウザ起動が行われる際、registerDeeplinkHandlerを実行してた場合には<br>
ブラウザからアプリに戻ってきてから`onReceived`メソッドが呼ばれます。<br>
この場合、ブラウザから復帰した際のActivityの`onResume`には必ず以下のように`Fox.trackDeeplinkLaunch`メソッドの実装を行ってください。<br>実装されていない場合、`onReceived`メソッドが呼ばれません。
```java
@Override
protected void onResume() {
    super.onResume();
    // 必ず呼んでください
    Fox.trackDeeplinkLaunch(this);
}
```


### 実装例1

デフォルトのDeeplinkHandlerでディープリンク情報を受信し、該当するActivityに遷移する処理の実装例となります。

```java
import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxTrackOption;
...

public class SampleActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FoxTrackOption mFoxTrackOption = new FoxTrackOption();
        mFoxTrackOption.addBuid(USER_ID)
                       .registerDeeplinkHandler(new FoxTrackOption.DeeplinkHandler() {
                            @Override
                            public void onReceived(JSONObject deeplinkInfo) {
                                // 以下、受信したディープリンク情報の操作例
                                if (deeplinkInfo != null) {
                                    String deeplinkStr = deeplinkInfo.optString("deeplink", "blank");
                                    Toast.makeText(SampleActivity.this, deeplinkStr, 0).show();
                                    if (!"blank".equals(deeplinkStr)) {
                                        Intent i = new Intent(Intent.ACTION_VIEW, Uri.parse(deeplinkStr));
                                        i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                                        startActivity(i);
                                  }
                                } else {
                                    Toast.makeText(CreateConversionActivity.this, "Could not received!!", 0).show();
                                }
                            }
                        });
        Fox.mFoxTrackOption(foxTrackOption);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // 必ず呼んでください
        Fox.trackDeeplinkLaunch(this);
    }

}
```

### 実装例2

以下は`DeeplinkHandler`クラスを拡張し、durationに３日を指定している実装例となります。

```java

import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxTrackOption;
...

public class SampleActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FoxTrackOption mFoxTrackOption = new FoxTrackOption();
        mFoxTrackOption.addBuid(USER_ID)
                       .registerDeeplinkHandler(new CustomDeeplinkHandler());
        Fox.mFoxTrackOption(foxTrackOption);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // 必ず呼んでください
        Fox.trackDeeplinkLaunch(this);
    }

    /*
     * DeeplinkHandlerの拡張クラス
     */
    class CustomDeeplinkHandler extends FoxTrackOption.DeeplinkHandler {

      @Override
      public long getDuration() {
          return 60 * 60 * 24 * 3;
      }

      @Override
      public void onReceived(@Nullable JSONObject deeplinkInfo) {
          if (deeplinkInfo != null) {
              String ddl = deeplinkInfo.optString("deeplink", "blank");
              Toast.makeText(ThisActivity.this, deeplinkStr, 0).show();
              if (!"blank".equals(deeplinkStr)) {
                  Intent i = new Intent(Intent.ACTION_VIEW, Uri.parse(deeplinkStr));
                  i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                  startActivity(i);
              }
          } else {
              Toast.makeText(CreateConversionActivity.this, "Could not received!!", 0).show();
          }
      }
    }
}    
```

---
[トップ](../../README.md)
