[TOP](../../README.md)　>　Deferred Deeplink的执行

---
[![F.O.X](http://img.shields.io/badge/F.O.X%20SDK-4.1.0%20〜-blue.svg?style=flat)](https://github.com/forceoperationx/public-fox-android-sdk/releases/tag/4.1.0)

# Deferred Deeplink的执行

通过安装Deferred Deeplink处理，可以在点击广告时跳转到绑定的Deferred Deeplink，以此来跳转到APP内的目标页面。此外，即使APP没有安装，也可以使用Deeplink让用户在经过广告安装进入APP后跳转到指定的页面。

> 支持版本: 4.1.0及以上

下面针对Defferred Deeplink的代码安装所需的类和方法进行说明。

## FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|返回值|方法|安装地点|说明|
|:---:|:---|:---:|:---|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)|onCreate|通过调用本方法可以在Install计测的时候去向服务器询问Defferred Deeplink。|

<div id="deeplinkhandler"></div>

## DeeplinkHandler

public static abstract class DeeplinkHandler

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption$DeeplinkHandler

这个类是FoxTrackOption类的InnerClass。

|返回值|类型|方法|安装地点|说明|
|:---:|:---:|:---|:---:|:---|
|DeeplinkHandler|-|**getDefault** ()|onCreate|返回执行默认处理的DeeplinkHandler。获得DeferredDeeplink之后、想要自动跳转到绑定的Activity的时候使用。<br>※跳转的时候的Intent里不能添加参数。|
|void|abstract|**onReceived** (JSONObject jObject)<br><br>`jObject` : 放置收到的Deeplink信息的JSONObject|onCreate|在收到DeferredDeeplink的时候为了取得使用的方法。DeferredDeeplink不存在或者通信失败的时候返回null。|
|long|-|**getDuration** ()|DeeplinkHandler扩张类内|[任意]在向服务器询问DeferredDeeplink的时候、作为对象的最终点击的对象期间(秒数)（用秒数指定追溯到多久以前）<br>默认: 86400 (1天)|

### 收取JSONObject的形式

使用DeeplinkHandler的onReceived方法的参数收取的Json是按照如下的形式。<br>
※ Deeplink的值是个例子。

```json
{
  "deeplink":"targetapp://details?id=12345&category=1"
}
```

## 注意事项

* **Cookie计测的场合**<br><br>
基于Cookie计测手法，浏览器跳转发生时，执行registerDeeplinkHandler的时候<br>
从浏览器跳转回APP之后调用`onReceived`方法。<br>
这种情况下、请在从浏览器恢复到Activity的`onResume`里，请一定按照如下所示来编码安装`Fox.trackDeeplinkLaunch`方法。<br>没有安装的话，将不会调用`onReceived`方法。
```java
@Override
protected void onResume() {
    super.onResume();
    // 请一定调用
    Fox.trackDeeplinkLaunch(this);
}
```

### 编码安装案例1

在收到Deeplink后跳转到关联画面的编码案例。<br>
如果不是有效的Deeplink、将无法跳转停留在那个画面。

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
        // 必ず呼んでください
        Fox.trackDeeplinkLaunch(this);
    }

}
```

### 编码安装案例2

在DeeplinkHandler的onReceived方法里编码安装任意处理的案例。<br>
下面记述的是收到Deeplink信息，并跳转到相应的Activity的处理的案例。

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
                                // 下面是收到Deeplink信息的操作案例
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
        // 请一定调用
        Fox.trackDeeplinkLaunch(this);
    }

}
```

### 编码安装案例3

下面是扩展了`DeeplinkHandler`类、将duration指定为３天的编码案例。

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
        // 请一定调用
        Fox.trackDeeplinkLaunch(this);
    }

    /*
     * DeeplinkHandler的扩展类
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
[Top](../../README.md)
