[TOP](../../README.md)　>　Deferred Deeplink Implementation

---

[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.1.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.1.0)

# Deferred Deeplink Implementation

Deferred Deep Linking occurs when a user clicks on a F.O.X tracker URL with a deep_link parameter in it, but does not have the app installed on the device at click time. This allows users to access a page within an app, without having it installed. Afterwards, the user will be redirected to the Play Store to download and install your app. After opening it for the first time, the deep_link parameter content will be delivered to your app.

> Supported Versions: 4.1.0 and above

Below is an explanation of the required classes and methods for deferred deep linking.

## FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|Return Value|Method|Target Area|Explanation|
|:---:|:---|:---:|:---|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)|onCreate|This method is called and queries the server for a deferred deep link when called.|

<div id="deeplinkhandler"></div>

## DeeplinkHandler

public static abstract class DeeplinkHandler

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption$DeeplinkHandler

This class is the FoxTrackOption class' inner class.

|Return Value|Type|Method|Target Area|Explanation|
|:---:|:---:|:---|:---:|:---|
|DeeplinkHandler|-|**getDefault** ()|onCreate|Returns the DeeplinkHandler which executes the default process. This is used when you want to seamlessly transition to the linked activity after receiving the deferred deep link.<br>※You cant add parameters to the Intent when transitioning|
|void|abstract|**onReceived** (JSONObject jObject)<br><br>`jObject` : A JSONObject which contains the received deep link data|onCreate|A method for receiving the deferred deep link. Thi method returns null of the is no deferred deep link or if the device has a communication failure|
|long|-|**getDuration** ()|DeeplinkHandler extension class|[Optional]When querying the deferred deep link to the server, this method returns the duration (in seconds) of the most recent click.  <br>Default: 86400 seconds (1 day)|

### The received JSON Object's Structure

The Json obejct received in the argument of the DeeplinkHandler's onReceived method has the following structure. <br>
※ The entered value is simply an example.

```json
{
  "deeplink":"targetapp://details?id=12345&category=1"
}
```

## Warnings

* **Cookie Tracking**<br><br>

When browsers are started during cookie measurement, if registerDeeplinkHandler is executed, the `onReceived` method is called after returning from the browser to the application.<br>
In this case, please implement the `Fox.trackDeeplinkLaunch` method in the `onResume` method of `Activity` when returning from the browser. If this is not implemented, the `onReceived` method will not be called.

```java
@Override
protected void onResume() {
    super.onResume();
    // Call this
    Fox.trackDeeplinkLaunch(this);
}
```

### Example 1

This is a scenario where a user is automatically transitioned to the linked screen after the deep link is received. <br> If it is not a valid deep link, the screen will not change.

```java
import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxTrackOption;
...

public class SampleActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FoxTrackOption mFoxTrackOption = new FoxTrackOption();
        mFoxTrackOption.registerDeeplinkHandler(FoxTrackOption.DeeplinkHandler.getDefault());
        Fox.trackInstall(mFoxTrackOption);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // Call this
        Fox.trackDeeplinkLaunch(this);
    }

}
```

### Example 2

This is a scenario where your app can implement whatever processing you set in the `onReceived` method of the `DeeplinkHandler`.
<br>
Here is the process of receiving deep link information and transitioning to the corresponding Activity.

```java
import co.cyberz.fox.Fox;
import co.cyberz.fox.FoxTrackOption;
...

public class SampleActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FoxTrackOption mFoxTrackOption = new FoxTrackOption();
        mFoxTrackOption.registerDeeplinkHandler(new FoxTrackOption.DeeplinkHandler() {
                            @Override
                            public void onReceived(JSONObject deeplinkInfo) {
                                // A deep link example process
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
        Fox.trackInstall(mFoxTrackOption);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // Call this
        Fox.trackDeeplinkLaunch(this);
    }

}
```

### Example 3

This is a scenario where the `DeeplinkHandler` is extended and specifies three days as the click duration.

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
        Fox.trackInstall(mFoxTrackOption);
    }

    @Override
    protected void onResume() {
        super.onResume();
        Call this
        Fox.trackDeeplinkLaunch(this);
    }

    /*
     * DeeplinkHandler extending class
     */
    class CustomDeeplinkHandler extends FoxTrackOption.DeeplinkHandler {

      @Override
      public long getDuration() {
          return 60 * 60 * 24 * 3;
      }

      @Override
      public void onReceived(@Nullable JSONObject deeplinkInfo) {
          if (deeplinkInfo != null) {
              String deeplinkStr = deeplinkInfo.optString("deeplink", "blank");
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
[Return to Top](../../README.md)
