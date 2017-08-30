[TOP](../../README.md)　>　自動計測の詳細

---

# 自動計測の詳細

* **[1. 自動計測について](#autotracking)**
* **[2. アクティベーションと自動計測の有効化](#autotracking_enable)**
* **[3. 自動計測オプション](#autotracking_option)**

## 1. 自動計測について

* 本機能はF.O.X Android SDK 4.3.0からのサポートとなります。
* 対象となる計測種類は以下3種類となります。
  * 初回起動計測
  * セッション計測
  * リエンゲージメント計測
  * ディファードディープリンキング
* その他の課金等のアプリ内イベントの計測は従来通りの実装となります。
* 一部の計測を手動で実装することも可能です。

## 2. アクティベーションと自動計測の有効化

アクティベーションには以下いずれかの`FoxConfig`を使用します。

### **2.1 FoxConfigインスタンス**
&nbsp;&nbsp;&nbsp;co.cyberz.fox.FoxConfigを使用します。<br>
&nbsp;&nbsp;&nbsp;〜 4.2.1まで使われてきたコンフィギュレーションクラスです。ここでの利用方法は省略します。<br>
&nbsp;&nbsp;&nbsp;詳細は[従来のアクティベーション](../README.md#old_activation)または[FoxConfig](../sdk_api/README.md#foxconfig)をご参照ください。

[&nbsp;**FoxConfigインスタンスで実装する例**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.annotation.FoxConfig;

public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDKアクティベーション、自動計測開始
      Fox.AUTOMATOR.init(config).startTrack();
  }

}
```

### **2.2 FoxConfigアノテーション**
&nbsp;&nbsp;&nbsp;co.cyberz.fox.annotation.FoxConfigを使用します。<br>
&nbsp;&nbsp;&nbsp;4.3.0より追加されたアノテーションで必須情報を定義するためのコンフィギュレーションクラスです。<br>
&nbsp;&nbsp;&nbsp;[FoxConfig API詳細](../sdk_api/README.md#annotation_foxconfig)

> * co.cyberz.fox.FoxConfigとco.cyberz.fox.annotation.FoxConfigは併用出来ません。

[&nbsp;**FoxConfigアノテーションで実装する例**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.annotation.FoxConfig;

@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();
      // SDKアクティベーション、自動計測開始
      Fox.AUTOMATOR.init(this).startTrack();
  }

}
```

> * FoxConfigアノテーションはApplicationクラスを継承したクラスに定義します。
>
> * Fox.AUTOMATORはApplication継承クラス内で実装してください。

<div id="autotracking_option"></div>

## 3. 自動計測オプション

### 3.1 FoxTrackOptionを用いてオプションを指定する

自動計測における計測オプションは、[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption)を使用します。

```java
@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isDebug = BuildConfig.DEBUG)
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxTrackOption option = new FoxTrackOption();
      option.setRedirectUrl("myapp://top")
            .setBuid(getUserId())
            .setTrackingStateListener(new FoxTrackOption.TrackingStateListener() {
               @Override
               public void onComplete() {
                 // 計測完了時に実行する処理

               }
      });
      Fox.AUTOMATOR.init(this).startTrack(option);
  }

}
```

> ※ 初回起動計測でBuidを指定する必要があり且つ、Application内ではBuid用意出来ない場合、個別に別途`Fox.trackInstall()`を実装する必要があります。実装先は従来と同様にMainとなるActivityのonCreate()となります。
> ※ リエンゲージメント計測でBuidを指定する必要があり且つ、Application内ではBuid用意出来ない場合、個別に別途`Fox.trackDeeplinkLaunch`を実装する必要があります。

### 3.2 特定の計測種別を手動実装する

自動計測対象から外して個別に計測の実装をすることが可能です。

#### 3.2.1 アノテーションでFoxConfigを定義している場合

co.cyberz.fox.annotation.FoxConfigの以下３つの設定を行います。いずれもデフォルトでtrueです。
* `isAutoInstallTracking` : 初回起動計測の自動計測実行有無の設定
* `isAutoSessionTracking` : セッション計測の自動計測実行有無の設定
* `isAutoDeeplinkTracking` : ディープリンク系の計測の自動計測実行有無の設定

**初回起動計測の自動計測をOFFにする**

`isAutoInstallTracking`をfalseに設定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isAutoInstallTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      // インストール計測実行
      Fox.trackInstall();
  }
...
```
> 従来通りアプリの最初に起動するActivityのonCreateに`[Fox.trackInstall()](../track_install/README.md)`を実装します。

**セッション計測の自動計測をOFFにする**

`isAutoSessionTracking`をfalseに設定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isAutoSessionTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // セッション計測
      Fox.trackSession();
  }
...
```
> 従来通り各ActivityのonResumeに`Fox.trackSession()`を実装します。

**ディープリンクによる計測の自動計測をOFFにする**

`isAutoDeeplinkTracking`をfalseに設定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
@FoxConfig(appId = 発行されたアプリID, appKey = "発行されたAPP_KEY", appSalt = "発行されたAPP_SALT", isAutoDeeplinkTracking = false)
public class YourApplication extends Application {
...
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // リエンゲージメント計測
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> 従来通りMainとなるActivityのonResumeに`Fox.trackDeeplinkLaunch()`を実装します。


#### 3.2.2 FoxConfigインスタンスを定義している場合

co.cyberz.fox.Fox.AUTOMATORの以下３つの設定を行います。いずれもデフォルトでfalseです。

* `setManualInstallTracking` : 初回起動計測の自動計測実行有無の設定
* `setManualSessionTracking` : セッション計測の自動計測実行有無の設定
* `setManualDeeplinkTracking` : ディープリンク系の計測の自動計測実行有無の設定

**初回起動計測の自動計測をOFFにする**

`setManualInstallTracking`にtrueを指定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDKアクティベーション、自動計測開始(初回起動計測のみ手動実装をON)
      Fox.AUTOMATOR.init(config)
                   .setManualInstallTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      // インストール計測実行
      Fox.trackInstall();
  }
...
```
> 従来通りアプリの最初に起動するActivityのonCreateに`[Fox.trackInstall()](../track_install/README.md)`を実装します。


**セッション計測の自動計測をOFFにする**

`setManualSessionTracking`にtrueを指定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDKアクティベーション、自動計測開始(セッション計測のみ手動実装をON)
      Fox.AUTOMATOR.init(config)
                   .setManualSessionTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // セッション計測
      Fox.trackSession();
  }
...
```
> 従来通り各ActivityのonResumeに`Fox.trackSession()`を実装します。

**ディープリンクによる計測の自動計測をOFFにする**

`setManualDeeplinkTracking`にtrueを指定し、個別に実装します。

[&nbsp;Application&nbsp;]
```java
public class YourApplication extends Application {

  @Override
  public void onCreate() {
      super.onCreate();

      FoxConfig config = new FoxConfig(this, 発行されたアプリID, "発行されたAPP_KEY", "発行されたAPP_SALT").addDebugOption(BuildConfig.DEBUG);
      // SDKアクティベーション、自動計測開始(ディープリンク計測のみ手動実装をON)
      Fox.AUTOMATOR.init(config)
                   .setManualDeeplinkTracking(true)
                   .startTrack();
  }
```
[&nbsp;Activity&nbsp;]
```java
public class YourActivity extends Activity {

  @Override
  protected void onResume() {
      super.onResume();
      // リエンゲージメント計測
      Fox.trackDeeplinkLaunch(this);
  }
...
```
> 従来通りMainとなるActivityのonResumeに`Fox.trackDeeplinkLaunch()`を実装します。


---
[トップ](../../README.md)
