## 依靠流量分析進行Event計測

能夠計測按不同廣告流入和自然流入的用戶在APP裡面引發的Event。請在想要做Event計測的全部地點追加代碼。

為了進行依據流量分析的Event計測，請安裝下面的sendEvent方法。

```java
import jp.appAdForce.android.AnalyticsManager;
```

利用下面的sendEvent方法來發送Event信息。

```java
public static void sendEvent(Context context, String eventName, String action, String label,
int value);
```

sendEvent方法的參數說明如下。


|參數|型|概要|
|:------|:------:|:------:|:------|
|context|Context|設定為MainActivity.this|
|eventName|String|設定能夠識別計測Event的任意名稱。|
|action|String|設定屬於Event的Action名。可以自由設定。可以為null。|
|label|String|設定屬於Event的Label名。可以自由設定。可以為null。|
|value|int|指定Event次數。可以為1。|



```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

  //Event成功時
  public void didTutorial()　{
    // Event發送
    AnalyticsManager.sendEvent(MainActivity.this, "教程突破", null, null, 1);
  }
}
```

---
[TOP](/lang/zh-tw/README.md)
