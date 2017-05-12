[TOP](../../README.md)　>　インストール計測の詳細

---

# インストール計測の詳細

* [1. インストール計測の実装](#track_install_basic)
* [2. インストール計測の実装(オプション指定)](#track_install_optional)
* [3. その他のインストール計測実装例](#track_install_other)

<div id="track_install_basic"></div>

## 1. インストール計測の実装

trackInstallメソッドを利用することで、インストール計測を行うことができます。Cookie計測を利用する場合には、外部ブラウザが起動されます。この際、外部ブラウザの遷移先をtrackInstallの引数にURL文字列を指定することができます。

プロジェクトのソースコードを編集し、アプリケーションの起動時に呼び出されるActivityに対して、次の通り実装を行ってください。


```java
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
	Fox.trackInstall();
}
```

> ※ 引数を指定せずtrackInstallメソッドを呼び出すと、F.O.X管理画面上での設定内容が優先されます。Cookie計測の際のリダイレクト先URLを実装内で指定する場合は、本項以降の説明をご確認ください。

> ※ trackInstallメソッドは、特に理由がない限りはアプリケーションの起動時に呼び出されるActivityのonCreateメソッド内に実装してください。それ以外の箇所に実装された場合にはインストール数が正確に計測できない場合があります。<br>
アプリケーションの起動時に呼び出されるMainのActivityのonCreateメソッド内に実装していない状態でインストール成果型の広告を実施する際には、必ず広告代理店もしくは媒体社の担当にその旨を伝えてください。正確に計測が行えない状態でインストール成果型の広告を実施された際には、計測されたインストール数以上の広告費の支払いを求められる恐れがあります。

![sendConversion01](./img01.png)

<div id="track_install_optional"></div>

## 2. インストール計測の実装(オプション指定)

インストール計測が完了したことをコールバックで受け取りたい場合、特定のURLヘ遷移させる場合や、アプリケーションで動的にURLを生成したい場合には、以下の[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption)クラスを使用します。<br>

```java
import co.cyberz.fox.FoxTrackOption;
import co.cyberz.fox.Fox;

@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  // 初回起動のインストール計測
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

@Override
protected void onResume() {
    super.onResume();
    // 必ず呼んでください
    Fox.trackDeeplinkLaunch(this);
}
```

> ※ 上記のサンプルコードでは、リダイレクト先・BUID・オプトアウトの有無・計測完了のコールバックを受け取る処理の実装例となっています。<br>TrackingStateListernerをセットした上で計測処理が完了するとonCompleteメソッドが呼ばれますので、インストール計測完了直後に実行したい処理はこちらに実装してください。

> ※ Cookie計測が有効な場合、TrackingStateListernerのonCompleteメソッドは、ブラウザからアプリに復帰した後に呼ばれます。その復帰したActivityのonResumeには必ず`Fox.trackDeeplinkLaunch`メソッドを実装してください。<br>実装していない場合、onCompleteメソッドが呼ばれません。
```java
@Override
protected void onResume() {
    super.onResume();
    // 必ず呼んでください
    Fox.trackDeeplinkLaunch(this);
}
```

> ※ オプトアウトを有効にした場合、その後そのユーザーを広告の配信対象から外すことが可能です。<br>
尚、オプトアウトはユーザーに対しオプトアウトの意思表示を選択させるような機能をアプリ内で実装している場合に有効です。

> ※ F.O.X SDKのAPI仕様は[こちら](../sdk_api/README.md)でご確認ください。

<div id="track_install_other"></div>

## 3. その他のインストール計測実装例

`Fox.trackInstall()`をApplication継承クラスに実装することで、起動計測系の処理を集約することが可能となります。<br>
但し、Activityが呼ばれる前に動作するためFoxTrackOptionを用いたBUIDの指定やオプトアウトの指定は難しい場合があります。そのため、Fingerprint計測やInstallReferrer計測などのCookie計測を行わない場合に有効です。

```java
import android.app.Application;
import co.cyberz.common.FoxConfig;
import co.cyberz.fox.Fox;

public class YourApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // アクティベーション処理
        private int FOX_APP_ID = 発行されたアプリID;
        private String FOX_APP_KEY = "発行されたAPP_KEY";
        private String FOX_APP_SALT = "発行されたAPP_SALT";
        new FoxConfig(this, FOX_APP_ID, FOX_APP_KEY, FOX_APP_SALT).activate();

        // アプリケーションのライフサイクルの検知
        registerActivityLifecycleCallbacks(new ApplicationLifeCycleCallbacks());
    }


    private static final class ApplicationLifeCycleCallbacks implements ActivityLifecycleCallbacks {

	    @Override
	    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
	      // インストール計測
	      Fox.trackInstall();
	    }

	    @Override
	    public void onActivityStarted(Activity activity) {
	    }

	    @Override
	    public void onActivityResumed(Activity activity) {
	      // セッショントラッキング
	      Fox.trackSession();
	      // リエンゲージメント計測
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
[トップ](../../README.md)
