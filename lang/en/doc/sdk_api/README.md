# Fox SDK API

<div id="foxconfig"></div>

# FoxConfig

public class FoxConfig

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxConfig

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxConfig** (Context c, int appId, String appKey, String appSalt)<br><br>`c` : Context (ApplicationContext allowed)<br>`appId` : issued App ID<br>`appKey` : issued App KEY<br>`appSalt` : issued Salt|Setting the minimal parameters necessary for F.O.X to operate|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|void|**activate** ( )|saves predetermined necessary information in the App|
|FoxConfig|**addDebugOption** (boolean isDebug)<br><br>`isDebug` : enable/disable debug log output|outputs error messages and stack traces to Logcat<br>`default : false`|
|FoxConfig|**addServerUrlOption** (String url)<br><br>`url` : DeliverServer encrypted string URL|used for debugging, and setting URLs when switching between HTTPS/HTTP. <br> ※not used unless an admin sets it.|
|FoxConfig|**addAServerUrlOption** (String url)<br><br>`url` : Access analytics Server encrypted string URL|used for debugging, and setting URLs when switching between HTTPS/HTTP.<br> ※not used unless an admin sets it.|
|FoxConfig|**addDServerUrlOption** (String url)<br><br>`url` : Dahlia Server encrypted string URL|used for debugging, and setting URLs when switching between HTTPS/HTTP.<br> ※not used unless an admin sets it.|

<div id="annotation_foxconfig"></div>

# FoxConfig

public interface FoxConfig

Added in 4.3.0

This interface defines some necessary information by annotation

### Public methods

|Type|Field|Details|
|:---:|:---|:---|
|int|appId|AppID value|
|String|appKey|AnalyticsAppKey value|
|String|appSalt|AppSalt value|
|String|serverUrl|server URL value(for debug)|
|String|aServerUrl|access analytics server URL value(for debug)|
|boolean|isDebug|enable/disable debug mode<br>※default is false|
|boolean|isAutoInstallTracking|enable/disable automatic install tracking<br>※default is true|
|boolean|isAutoDeeplinkTracking|enable/disable automatic deeplink tracking<br>※default is true|
|boolean|isAutoSessionTracking|enable/disable access analytics automatic session tracking<br>※default is true|

<div id="fox"></div>

# Fox

public enum Fox<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;	java.lang.Enum&lt;E extends [java.lang.Enum](https://developer.android.com/reference/java/lang/Enum.html)&lt;E&gt;&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.Fox

### Public instance

* **`AUTOMATOR`** : Used when implementing auto-tracking features &nbsp;(Added in 4.3.0)

### Public methods

|**Return**|**Type**|**Method**|**Details**|
|:---:|:---:|:---|:---|
|void|static|**trackInstall** ( )|tracks first launch post install.<br> executes as specified in the F.O.X management portal.|
|void|static|**trackInstall** ([FoxTrackOption](#foxtrackoption) option)<br><br>`option` :user defined options|tracks first launch post install. <br>executes according to `option`s specified|
|void|static|**trackSession** ( )|initalized in `onResume`, then tracks session|
|void|static|**trackEvent** ([FoxEvent](#foxevent) event)<br><br>`event` : class that sets multiple parameters needed for tracking events|tracks App events.|
|void|static|**trackEventByBrowser** (String url)<br><br>`url` : web page URL embedded with an event tag|tracks events with an event tag embedded into a web page URL.|
|void|static|**trackEventByWebView** (WebView webView)<br><br>`webView` : Webview which accesses an LTV tracking webpage|LTV tracking by Webview. As of Android L, editing third party cookies into a webview requires user permission, thereby requiring a webview argument.|
|boolean|static|**isConversionCompleted** ( )|returns a boolean value determining whether install tracking has been completed.|
|void|static|trackDeeplinkLaunch (Intent intent)<br><br>`intent` : Intent launched by a custom URL Scheme| ・Re-engagement tracking <br> ・Detects first time launch tracking completion <br>・Controls receipt of deferred deeplink|
|void|static|trackDeeplinkLaunch (Intent intent, String buid)<br><br>`intent` : Intent launched by a custom URL Scheme<br>`buid` : advertiser device ID (e.g. user ID)|・Re-engagement tracking <br> ・Detects first time launch tracking completion <br>・Controls receipt of deferred deeplink|
|void|static|trackDeeplinkLaunch (Activity activity)<br><br>`activity` : Intent launched by a custom URL Scheme|・Re-engagement tracking <br> ・Detects first time launch tracking completion <br>・Controls receipt of deferred deeplink|
|void|static|trackDeeplinkLaunch (Activity activity, String buid)<br><br>`activity` : Intent launched by a custom URL Scheme<br>`buid` : advertiser device ID (e.g. user ID)|・Re-engagement tracking <br> ・Detects first time launch tracking completion <br>・Controls receipt of deferred deeplink|
|void|static|**setUserInfo** (JSONObject userInfo)<br><br>`userInfo` : user information/traits|sets the desired trackable user information/traits|
|JSONObject|static|**getUserInfo** ( )|getter for setUserInfo|
|Fox|-|**init** ( Context context )|an init method that is called when required information is set by annotation.<br>(Added in 4.3.0)|
|Fox|-|**init** ( [FoxConfig](#foxconfig) config )|manually calls init method<br>※cannot be used in conjuction with setting annotated data<br>(Added in 4.3.0)|
|-|-|**startTrack** ( ) |runs auto-tracking<br>(Added in 4.3.0)|
|-|-|**startTrack** ( FoxTrackOption option ) |runs auto-tracking with options<br>(Added in 4.3.0)|
|Fox|-|**setManualInstallTrackingEnable** ( boolean manualInstallTracking ) <br><br>`manualInstallTracking` : flag for manual install tracking execution|use when performing manual install tracking. <br> default : false(auto-tracking enabled)<br>(Added in 4.3.0)|
|boolean|-|**isManualInstallTrackingEnable** ( ) |returns a boolean determining whether manual install tracking has been enabled<br>(Added in 4.3.0)|
|Fox|-|**setManualDeeplinkTrackingEnable** ( boolean manualDeeplinkTracking ) <br><br>`manualDeeplinkTracking` : flag for manual deeplink tracking execution|use when performing manual deeplink tracking <br> default : false(auto-tracking enabled)<br>(Added in 4.3.0)|
|boolean|-|**isManualDeeplinkTrackingEnable** ( ) |returns a boolean determining whether manual deeplink tracking has been enabled <br>(Added in 4.3.0)|
|Fox|-|**setManualSessionTrackingEnable** ( boolean manualSessionTracking ) <br><br>`manualSessionTracking` : flag for manual session tracking execution| use when performing manual session tracking <br> default : false(auto-tracking enabled)<br>(Added in 4.3.0)|
|boolean|-|**isManualSessionTrackingEnable** ( ) |returns a boolean determining whether manual session tracking has been enabled<br>(Added in 4.3.0)|

<div id="foxtrackoption"></div>

# FoxTrackOption

public class FoxTrackOption<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxTrackOption

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|init|**FoxTrackOption** ()|Constructor|
|FoxTrackOption|**setRedirectUrl** (String redirectTo)<br><br>`redirectTo` : redirect destination after browser launch|sets the URL/URL Scheme which will be used as a redirect destination from a browser when performing Cookie tracking.|
|FoxTrackOption|**setBuid** (String buid)|Buid setter|
|FoxTrackOption|**setOptOut** (boolean optOut)|OptOut toggler<br>※default : false|
|void|**setTrackingStateListener** (TrackingStateListener listener)|Postback method which runs when a first time install is successfully tracked. Overrides the `onComplete` method.|
|FoxTrackOption|**registerDeeplinkHandler** ([DeeplinkHandler](#deeplinkhandler) handler)<br><br>`handler` : handler for receiving deferred deeplinks|called when using deferred deeplink.|


<div id="foxevent"></div>

# FoxEvent

public class FoxEvent<br>

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.FoxEvent

### Public constructors

|**Constructor**|**Details**|
|:---|:---|
|**FoxEvent** (String eventName)|measures access analytics events|
|**FoxEvent** (String eventName, int ltvPointId)|measures access analytics events and conversion points|

### Public methods

|**Return**|**Method**|**Details**|
|:---:|:---|:---|
|FoxEvent|**addExtraInfo** (String key, String val)|set an arbitrary key/value pair|
|HashMap<String, String>|**getExtraInfo** ( )|gets a HashMap containing any key/value pairs added with addExtraInfo|
|JSONObject|**getEventInfo** ( )|returns a JSON object containted within `eventInfo` during engagement measurement.|

### Public variables

|**Type**|**Variable**|**Details**|
|:---:|:---|:---|
|String|buid|sets Buid|
|double|price|sets price|
|String|currency|sets currency|
|String|sku|sets sku|
|String|value|sets value|
|String|label|sets label|
|String|orderId|sets orderId|
|int|quantity|sets quantity|
|JSONObject|eventInfo|sets eventInfo|

<div id="ids"></div>

# Ids

public class Ids

[java.lang.Object](https://developer.android.com/reference/java/lang/Object.html)<br>
&nbsp;&nbsp;&nbsp;↳&nbsp;co.cyberz.fox.extra.Ids

### Public methods

|**Type**|**Return**|**Method**|**Details**|
|:---:|:---:|:---|:---|
|static|String|*static*<br>**get** ( Context context, String key)|insert an arbitrary ID name for `key` <br>例：`get(getApplicationContext(), "xuniq")`|


---
[Return to Top](../../README.md)
