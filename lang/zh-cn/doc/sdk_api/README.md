# Fox SDK API

<div id="foxconfig"></div>

# FoxConfig

public class FoxConfig

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxConfig

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxConfig** (Context c, int appId, String appKey, String appSalt)<br><br>`c` : Context (ApplicationContext可)<br>`appId` : 在管理画面发行的APP ID<br>`appKey` : 在管理画面发行的APP　KEY<br>`appSalt` : 在管理画面发行的SALT|设置让FOX SDK运作所必须的参数|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|void|**activate** ( )|在内部保存设置的必要信息|
|FoxConfig|**addDebugOption** (boolean isDebug)<br><br>`isDebug` : 是否输出Debug日志|设置发生错误时的消息和堆栈轨迹（Stack Trace）输出到Logcat<br>默认： false|
|FoxConfig|**addServerUrlOption** (String url)<br><br>`url` : Deliver服务器URL加密字符串|用于debug或用于切换HTTPS/HTTP的URL&nbsp;SETTER方法<br>※必须由管理员指定权限后才能使用|
|FoxConfig|**addAServerUrlOption** (String url)<br><br>`url` : 流量分析服务器URL加密字符串|用于debug或用于切换HTTPS/HTTP的URL&nbsp;SETTER方法<br>※必须由管理员指定权限后才能使用。|
|FoxConfig|**addDServerUrlOption** (String url)<br><br>`url` : Dahlia服务器URL加密字符串|用于debug或用于切换HTTPS/HTTP的URL&nbsp;SETTER方法<br>※必须由管理员指定权限后才能使用。|

<div id="fox"></div>

# Fox

public class Fox<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.Fox

### Public methods

|**Return**|**Type**|**Method**|**Details**|
|:---:|:---:|:---|:---|
|void|static|**trackInstall** ( )|计测安装后首次启动的方法。根据管理界面中的设置来执行。|
|void|static|**trackInstall** ([FoxTrackOption](#foxtrackoption) option)<br><br>`option` : 计测指定选项|计测安装后首次启动的方法。根据FoxOption设置的计测选项来执行。|
|void|static|**trackSession** ( )|在onResume里安装执行Session计测。|
|void|static|**trackEvent** ([FoxEvent](#foxevent) event)<br><br>`event` : 计测事件时各种必须参数的设置类|计测APP内事件|
|void|static|**trackEventByBrowser** (String url)<br><br>`url` : 埋入Event Tag的外部网页的URL链接|设置了Event Tag的外部网页的事件计测|
|void|static|**trackEventByWebView** (WebView webView)<br><br>`webView` : 访问发生LTV的页面的WebView|使用WebView进行Tag计测。Android L之后的版本为了在WebView里修改第三方Cookie，需要在参数中设置WebView才能获得权限。|
|boolean|static|**isConversionCompleted** ( )|通过boolean值来反馈Install计测完成与否|
|void|static|trackDeeplinkLaunch (Intent intent)<br><br>`intent` : 使用自定义URL scheam方式启动时的Intent|对流失唤回广告计测，监测探知初回启动计测是否完成，Deferred Deeplink的取得进行控制|
|void|static|trackDeeplinkLaunch (Activity activity)<br><br>`activity` : 使用自定义URL scheam方式唤起的Activity|对流失唤回广告计测，监测探知初回启动计测是否完成，Deferred Deeplink的取得进行控制|
|void|static|**setUserInfo** (JSONObject userInfo)<br><br>`userInfo` : 用户信息・属性|设置计测时候的用户信息・属性等|
|JSONObject|static|**getUserInfo** ( )|用setUserInfo设置了用户信息时，可以获取该用户信息|

<div id="foxtrackoption"></div>

# FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|init|**FoxTrackOption** ()|构造函数|
|FoxTrackOption|**setRedirectUrl** (String redirectTo)<br><br>`redirectTo` : 浏览器启动后的跳转目的地|使用Cookie计测手法时，需指定自动跳转的目的地。URL/URL Scheme。|
|FoxTrackOption|**setBuid** (String buid)|buid的setter方法|
|FoxTrackOption|**setOptOut** (boolean optOut)|设置OptOut有无的setter方法<br>※默认 : false|
|void|**setTrackingStateListener** (TrackingStateListener listener)|首次启动计测成功时的回调函数。重写onComplete方法。|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)<br><br>`handler` : 为了获得DeferredDeeplink的handler|利用DeferredDeeplink的时候调用。|


<div id="foxevent"></div>

# FoxEvent

public class FoxEvent<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxEvent

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxEvent** (String eventName)|进行流量分析事件计测|
|**FoxEvent** (String eventName, int ltvPointId)|进行LTV计测和流量分析的事件计测|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|FoxEvent|**addExtraInfo** (String key, String val)|设置任意的key/value|
|HashMap<String, String>|**getExtraInfo** ( )|用addExtraInfo添加的key/value可以用HashMap取得|
|JSONObject|**getEventInfo** ( )|流失唤回广告计测时返回放入`eventInfo`里的JSONObject|

### Public variables

|**Type**|**Variable**|**Details**|
|:---:|:---|:---|
|String|buid|设置Buid|
|double|price|设置金額|
|String|currency|设置通貨|
|String|sku|设置sku|
|String|value|设置value|
|String|label|设置label|
|String|orderId|设置orderId|
|int|quantity|设置quantity|
|JSONObject|eventInfo|设置eventInfo|

---
[Top](../../README.md)
