## アクセス解析によるイベント計測

自然流入経由を含めた広告別にユーザーがアプリ内で起こすイベントを計測することが出来ます。イベントを計測したいすべての地点にコードを追加します。

アクセス解析によるイベント計測を行うために、次のsendEventメソッドを実装します。

```java
import jp.appAdForce.android.AnalyticsManager;
```

次のsendEventメソッドを利用し、イベント情報を送信します。

```java
public static void sendEvent(Context context, String eventName, String action, String label,
int value);
```

sendEventメソッドのパラメータの仕様は下記の通りです。

|パラメータ|型|概要|
|:------|:------:|:------:|:------|
|eventName|String|計測を行うイベントを識別できる任意の名前を設定します。イベント名は自由に設定可能です。|
|action|String|イベントに属するアクション名を設定します。アクション名は⾃由に設定可能です。nullでも構いません。|
|label|String|イベントに属するラベル名を設定します。ラベル名は⾃自に設定可能です。nullでも構いません。|
|value|int|イベント回数を指定します。1でも構いません。|



```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

  //イベント成功時
  public void didTutorial()　{
    // イベントの送信
    AnalyticsManager.sendEvent(MainActivity.this, "チュートリアル突破", null, null, 1);
  }
}
```

---
[トップ](/lang/ja/README.md)
