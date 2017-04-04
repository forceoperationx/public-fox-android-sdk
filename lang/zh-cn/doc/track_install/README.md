[TOP](../../README.md)　>　Install计测的详细

---

# Install计测的详细

* [1. Install计测的安装](#track_install_basic)
* [2. Install计测的安装(指定option)](#track_install_optional)
* [3. 其他的Install计测安装案例](#track_install_other)
* [4. 使用Web Tag的计测](#track_webtag)

<div id="track_install_basic"></div>

## 1. Install计测的安装

使用onLaunch方法可以进行Install计测。使用Cookie计测手法时会弹跳出外部浏览器。外部浏览器的迁移目的地可以通过在onLaunch的参数中指定URL字符串来实现。

编辑项目中的代码，针对APP启动时呼出的Activity，请按照以下内容来编码。


```java
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
	Fox.trackInstall();
}
```

> ※ 未指定参数调用trackInstall方法时，将优先使用F.O.X管理页面上的设置内容。在编写代码当中指定Cookie计测手法时跳转目的地URL时，请参考本章以后的说明。

> ※ 没有特殊理由的情况下，请在启动APP时呼出Activity的onCreate方法中编码实现trackInstall方法。在其他位置编码可能会导致Install数无法正确计测。<br>
APP启动时呼出Main的Activity的onCreate方法中未编码的状态下实行Install成果型广告时，必须向广告代理店或媒体单位进行说明。未正确进行计测就开始投放Install成果型广告，可能会产生超过计测Install数的广告费用。

![sendConversion01](./img01.png)

<div id="track_install_optional"></div>

## 2. Install计测的安装(指定option)

想要用Callback来获取Install计测完成后的信息、跳转到特定的URL、用APP动态生成URL时，请使用下面的[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption)类。<br>

```java
import co.cyberz.fox.FoxTrackOption;
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  // 初次启动的Install计测
  FoxTrackOption option = new FoxTrackOption();
  option.setRedirectUrl("myapp://top")
        .setBuid(getUserId())
        .setOptOut(true)
        .setTrackingStateListener(new FoxTrackOption.TrackingStateListerner() {
           @Override
           public void onComplete() {
             showTutorialDialog();
           }
        });
  Fox.trackInstall(option);
}
```

> 上述示例代码是一个，跳转目的地・BUID・有无optout・计测完成的callback受理的编码案例。<br>设置TrackingStateListerner后完成计测处理时会调用onComplete方法，请在Install计测完成后再次做编码处理。

> optout为有效时，可以将用户从广告投放对象中移除。<br>另外，optout仅在APP中已有用户可选optout功能的情况下有效。

> F.O.X SDK的API式样说明请确认[这里](../sdk_api/README.md)。

<div id="track_install_other"></div>

## 3. 其他的Install计测安装案例

在Application继承类中执行`Fox.trackInstall()`，可以将启动计测方面的处理集中汇总。<br>
但由于在调用Activity前进行处理，很难使用FoxTrackOption进行指定BUID或指定Optout。因此，在Fingerprint计测和InstallReferrer计测等Cookie计测手法以外时有效。

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // 激活处理
        private int FOX_APP_ID = 发行的APP ID;
        private String FOX_APP_KEY = "发行的APP KEY";
        private String FOX_APP_SALT = "发行的APP SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // APP的生命周期的检查
        registerActivityLifecycleCallbacks(new ApplicationLifeCycleCallbacks());
    }


    private static final class ApplicationLifeCycleCallbacks implements ActivityLifecycleCallbacks {

	    @Override
	    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
	      // Install计测
	      Fox.trackInstall();
	    }

	    @Override
	    public void onActivityStarted(Activity activity) {
	    }

	    @Override
	    public void onActivityResumed(Activity activity) {
	      // Session tracking
	      Fox.trackSession();
	      // 流失唤回广告计测
	      Fox.trackDeeplinkLaunch(activity);
	    }

	    @Override
	    public void onActivityPaused(Activity activity) {
	    }

	    @Override
	    public void onActivityStopped(Activity activity) {
	    }

	    @Override
	    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
	    }

	    @Override
	    public void onActivityDestroyed(Activity activity) {
	    }
  }
}
```

<div id="track_webtag"></div>

## 4. 使用Web Tag的计测

在网页上进行会员登录和商品购买等行为时，可以使用img Tag来进行LTV计测。<br>
<br>
F.O.X的LTV计测适用于外部浏览器和App内WebView。外部浏览器使用trackEventByBrowser方法，App内WebView使用trackEventByWebView方法，F.O.X会在LTV计测中将必要的信息记录在浏览器的Cookie里。

### 4.1 使用外部浏览器进行LTV计测

从APP跳转至外部浏览器，在外部浏览器的网页中进行tag计测时，请使用trackEventByBrowser方法启动外部浏览器。<br>
参数中用字符串指定外部浏览器要跳转的URL。

```java
import co.cyberz.fox.Fox;

...

Fox.trackEventByBrowser("http://www.mysite.com/event/");
```

### 4.2 使用App内WebView进行LTV计测

用户在WebView内进行跳转时，可以使用trackEventWebView方法进行计测。请在生成WebView的位置运行下列代码。多次生成・废除WebView时，必须每次运行trackEventByWebView方法。内部会用android.webkit.CookieManager和android.webkit.CookieSyncManager设置Cookie。<br>
另外，Android L版本中为了在每个WebView实例中获得写入第三方Cookie的许可，参数中必须传递WebView。

```java
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  WebView mWebView = (WebView) findViewById(R.id.sample_webview);
	Fox.trackEventByWebView(mWebView);
	mWebView.loadUrl("http://www.mysite.com/event/");
}
```

### 4.3 Tag的安装

请在LTV成果地点的页面中埋入计测tag。计测tag由本公司管理员联系提供。<br>
Tag中可使用的参数如下。

|参数名|必须|备注|
|:-----|:-----|:-----|
|_buyer|必須|识别广告主的ID。<br />由管理员联系提供，请输入提供的值。|
|_cvpoint|必須|成果地点を識別するID。<br />由管理员联系提供，请输入提供的值。|
|_price|可选|付费额。付费计测时请指定。|
|_currency|可选|半角英文数字的3文字的货币代码。<br />付费计测时请指定。<br />没指定的时候，_price会默认为JPY(日元)。|
|_buid|可选|最大64个半角英文数字。<br />保持会员ID等用户唯一值时请使用。|

> _currency请指定[ISO 4217](http://ja.wikipedia.org/wiki/ISO_4217)里定义的货币代码。

---
[Top](../../README.md)
