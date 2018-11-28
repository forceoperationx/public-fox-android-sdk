[TOP](../../README.md)　>　Install Tracking details

---

# Install Tracking details

* [1. Implementing Install Tracking](#track_install_basic)
* [2. Implementing Install Tracking with Options](#track_install_optional)
* [3. Install Tracking Examples](#track_install_other)

<div id="track_install_basic"></div>

## 1. Implementing Install Tracking

You can track installs with the `trackInstall` method. When performing Cookie tracking, external browsers are used and you can include a URL as an argument of the `trackInstall` method.

With regards to changing the Activity that is called for app launch, please see the following implementation.

```java
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
	Fox.trackInstall();
}
```
> ※ If you call the `trackInstall` method without specifying an argument, the settings on the F.O.X management portal will take precedence. If you are specifying a redirect URL for Cookie tracking, see the following explanation.

> ※ The trackInstall method should be implemented in the onCreate method of the Activity called when the application is launched unless there is a particular reason. If it is included in other places, the install tracking results may not be accurate. <br> Be sure to notify your advertising agency or media company if you have not implemented the `trackInstall` method in the `onCreate` method of your Main Activity invoked at app launch. Otherwise there is a risk of being charged more for advertising expenses due to the possibility of falsely increased install numbers.

![sendConversion01](./img01.png)

<div id="track_install_optional"></div>

## 2. Implementing Install Tracking with Options

If you want to receive callbacks for install completions, redirect to arbitrary URLs, or dynamically generate URLs in your app, use the following [`FoxTrackOption`](../sdk_api/README.md#foxtrackoption) class.

```java
import co.cyberz.fox.FoxTrackOption;
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  // First time launch tracking
  FoxTrackOption option = new FoxTrackOption();
  option.setRedirectUrl("myapp://top")
        .setBuid(getUserId())
        .setOptOut(true)
        .setTrackingStateListener(new FoxTrackOption.TrackingStateListener() {
           @Override
           public void onComplete() {
             showTutorialDialog();
           }
        });
  Fox.trackInstall(option);
}

@Override
protected void onResume() {
    super.onResume();
    // Call this
    Fox.trackDeeplinkLaunch(this);
}
```

> ※ The above sample code demonstrates how to set redirect URLs, set a BUID, enable the OptOut feature, and receive callbacks for tracking completions. <br>
If there is any source that you would like to run immediately after setting the `TrackingStateListener` and successfully tracking an install, please run it in the `onComplete` method that will be called.

> ※ If Cookie tracking is enabled, The `onComplete` method of `TrackingStateListener` will be called after the user is returned from the browser to the app. Make sure that the `Fox.trackDeeplinkLaunch` is implemented in the `onResume` method of the Activity to which your browser returns your app.
If it is not implemented this way, the `onComplete` method will not be called.


```java
@Override
protected void onResume() {
    super.onResume();
    // Call this
    Fox.trackDeeplinkLaunch(this);
}
```

> ※ If the OptOut feature is enabled, that user will no longer be targeted by advertisements.
However, The OptOut feature is only available if your application requires users to choose whether or not to opt out.

> ※ For more details see the [F.O.X SDK API](../sdk_api/README.md).

<div id="track_install_other"></div>

## 3. Install Tracking Examples

By implementing the `Fox.trackInstall()` method in your `Application` class, you can track installs, sessions, and deeplink launches all from the same place.
However, because it is run before the Activity is generated, it is not possible to run the method with a FoxTrackOption that contains a BUID or OptOut settings (those values need to be obtained from the server).
You can set those options if you are not performing Cookie tracking, Fingerprint tracking, or installReferrer tracking.

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // Activation source
        private int FOX_APP_ID = 発行されたアプリID;
        private String FOX_APP_KEY = "発行されたAPP_KEY";
        private String FOX_APP_SALT = "発行されたAPP_SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // Application LifeCycle detection
        registerActivityLifecycleCallbacks(new ApplicationLifeCycleCallbacks());
    }


    private static final class ApplicationLifeCycleCallbacks implements ActivityLifecycleCallbacks {

	    @Override
	    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
	      // Install tracking
	      Fox.trackInstall();
	    }

	    @Override
	    public void onActivityStarted(Activity activity) {
	    }

	    @Override
	    public void onActivityResumed(Activity activity) {
	      // session tracking
	      Fox.trackSession();
	      // Re-engagement measurement
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

---
[Return to Top](../../README.md)
