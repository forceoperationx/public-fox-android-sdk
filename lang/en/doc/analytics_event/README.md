## Event tracking by using access analytics

You can measure comparison of Installation number of organic and non-organic, the number of the of activating application and unique users (DAU / MAU), user retention and so on. When the access analytics is not required, you can omit the implementation of this item.

To perform access analytics, make any procedure in the following.

```java
import jp.appAdForce.android.AnalyticsManager;
```

In the case of event measurement such as completion of tutorial and member registration, please note like below.

```java
public static void sendEvent(Context context, String eventName, String action, String label,
int value);
```

Specification of the arguments of the `sendEvent` is as follows.

|Parameter|Type|Outline|
|:------|:------|:------|
|eventName|String|Set the arbitrary name identifying event conducting tracking. It is able to set event name freely.|
|action|String|Set the action name belonging to event. It is able to set action name freely. If not specifying, null is acceptable.|
|label|String|Set the label name belonging to action. It is able to set label name freely. If not specifying, null is acceptable.|
|value|int|Set the number of events. Basically, please set 1.|

```java
import jp.appAdForce.android.AnalyticsManager;

public class MainActivity extends Activity {

  public void didTutorial()　{
    // send Event
    AnalyticsManager.sendEvent(MainActivity.this, "tutorial completion", null, null, 1);
  }
}
```

---
[TOP](/lang/en/README.md)
