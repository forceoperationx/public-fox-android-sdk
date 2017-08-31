[TOP](../../README.md)　>　自動計測の詳細

---

# 自動計測の詳細

* **[1. 自動計測について](#about_autotracking)**
	* [1.1 旧バージョンからのマイグレーション](#migration)
* **[2. SDKのアクティベーションと自動計測の有効化](#autotracking_enable)**
	* [2.1 FoxConfigインスタンス](#foxconfig_instance)
	* [2.2 FoxConfigアノテーション](#foxconfig_annotation)
* **[3. 自動計測オプション](#autotracking_option)**
	* [3.1 FoxTrackOptionを用いてオプションを指定する](#autotracking_with_option)
	* [3.2 特定の計測種別を個別に実装する](#autotracking_with_manual)
		* [3.2.1 アノテーションでFoxConfigを定義している場合](#define_foxconfig_annotation)
			* [初回起動計測の自動計測をOFFにする](#off_annotation_auto_install_tracking)
			* [セッション計測の自動計測をOFFにする](#off_annotation_auto_session_tracking)
			* [ディープリンクによる計測の自動計測をOFFにする](#off_annotation_auto_deeplink_tracking)
		* [3.2.2 FoxConfigインスタンスを定義している場合](#define_foxconfig_instance)
			* [初回起動計測の自動計測をOFFにする](#off_instance_auto_install_tracking)
			* [セッション計測の自動計測をOFFにする](#off_instance_auto_session_tracking)
			* [ディープリンクによる計測の自動計測をOFFにする](#off_instance_auto_deeplink_tracking)


<div id="about_autotracking"></div>

## 1. 自動計測について

* 本機能は**F.O.X Android SDK 4.3.0からのサポート**となります。
* 対象となる計測種類は以下3種類となります。
  * 初回起動計測
  * セッション計測
  * リエンゲージメント計測
* その他の課金等のアプリ内イベントの計測は従来通りの実装となります。
* 一部の計測を手動で実装することも可能です。
* ディファードディープリンキングとの併用が可能です。

<div id="migration"></div>

### 1.1 旧バージョンからのマイグレーション

F.O.X Android SDK 4.X.Xをお使いで自動計測に変更される場合は以下を参考に変更ください。新規で導入される場合、本項はご参照されなくても結構です。<br>
それまでF.O.X Android SDK 3.X.Xを利用されていた場合はまず[最新バージョンへのマイグレーションについて](../migration/README.md)をご参照ください。<br>
以下、自動計測の対象となる3つの計測種別について説明します。

#### [&nbsp;初回起動計測&nbsp;]

アプリが起動したタイミングで自動で初回起動計測が行われます。<br>
これまでアプリが起動して最初に表示されるActivityのonCreateに実装されていたFox.trackInstall()は削除ししてください。

[&nbsp;削除&nbsp;]

```java
Fox.trackInstall();
```

> ※ 初回起動計測のみ自動計測の対象から外したい場合は&nbsp;[`3.2 特定の計測種別を個別に実装する`](#autotracking_with_manual)をご参照ください。

#### [&nbsp;セッション計測&nbsp;]

従来（Activityの遷移時に計測）とは異なり、アプリの起動時・バックグラウンドからの復帰時に自動で行われます。<br>
これまで全てのActivityのonResumeに実装されていたFox.trackSession()は削除してください。

[&nbsp;削除&nbsp;]

```java
Fox.trackSession();
```

> ※ セッション計測のみ自動計測の対象から外したい場合は&nbsp;[`3.2 特定の計測種別を個別に実装する`](#autotracking_with_manual)をご参照ください。

#### [&nbsp;ディープリンクによる計測&nbsp;]

リエンゲージメント広告の計測を行うためには、従来通りAndroidManifest.xmlに定義されているActivityに[`カスタムURLスキームの設定`](../../README.md#setting_urlscheme)がされていることが前提となります。<br>
これまでアプリが起動して最初に表示されるActivityのonResume()に実装されていたFox.trackDeeplinkLaunch()は削除してください。

[&nbsp;削除&nbsp;]

```java
Fox.trackDeeplinkLaunch(this);
```

> ※ ディープリンクによる計測のみ自動計測の対象から外したい場合は&nbsp;[`3.2 特定の計測種別を個別に実装する`](#autotracking_with_manual)をご参照ください。

<div id="autotracking_enable"></div>

## 2. SDKのアクティベーションと自動計測の有効化

アクティベーションには以下いずれかの`FoxConfig`を使用します。

<div id="foxconfig_instance"></div>

### **2.1 FoxConfigインスタンス**
&nbsp;&nbsp;&nbsp;[co.cyberz.fox.FoxConfig](../sdk_api/README.md#foxconfig)を使用します。<br>
&nbsp;&nbsp;&nbsp;〜 4.2.1まで使われてきたコンフィギュレーションクラスです。ここでの利用方法は省略します。<br>
&nbsp;&nbsp;&nbsp;詳細は[従来のアクティベーション](../README.md#old_activation)または[FoxConfig](../sdk_api/README.md#foxconfig)をご参照ください。

[&nbsp;**FoxConfigインスタンスで実装する例**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
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

<div id="foxconfig_annotation"></div>

### **2.2 FoxConfigアノテーション**
&nbsp;&nbsp;&nbsp;[co.cyberz.fox.annotation.FoxConfig](../sdk_api/README.md#annotation_foxconfig)を使用します。<br>
&nbsp;&nbsp;&nbsp;4.3.0より追加されたアノテーションで必須情報を定義するためのコンフィギュレーションクラスです。<br>
&nbsp;&nbsp;&nbsp;[FoxConfig API詳細](../sdk_api/README.md#annotation_foxconfig)

> * `co.cyberz.fox.FoxConfig`と`co.cyberz.fox.annotation.FoxConfig`の併用は出来ません。

[&nbsp;**FoxConfigアノテーションで実装する例**&nbsp;]

```java
import android.app.Application;
import co.cyberz.fox.Fox;
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

> * FoxConfigアノテーションは必ずApplication継承クラスに定義してください。
>
> * Fox.AUTOMATORはApplication継承クラス内で実装してください。

<div id="autotracking_option"></div>

## 3. 自動計測オプション

<div id="autotracking_with_option"></div>

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

> ※ 初回起動計測でBuidを指定する必要があり且つ、Application内ではBuid用意出来ない場合、別途`Fox.trackInstall()`を実装すること可能です。。実装先は従来と同様にMainとなるActivityのonCreate()となります。
>
> ※ リエンゲージメント計測でBuidを指定する必要があり且つ、Application内ではBuid用意出来ない場合、別途`Fox.trackDeeplinkLaunch`を実装する必要があります。

<div id="autotracking_with_manual"></div>

### 3.2 特定の計測種別を個別に実装する

自動計測対象から外して個別に計測の実装をすることが可能です。

<div id="define_foxconfig_annotation"></div>

#### 3.2.1 アノテーションでFoxConfigを定義している場合

co.cyberz.fox.annotation.FoxConfigの以下３つのフィールドを用いて設定を行います。いずれもデフォルトでtrueです。
* `isAutoInstallTracking` : 初回起動計測の自動計測実行有無の設定
* `isAutoSessionTracking` : セッション計測の自動計測実行有無の設定
* `isAutoDeeplinkTracking` : ディープリンク系の計測の自動計測実行有無の設定

<div id="off_annotation_auto_install_tracking"></div>

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
> 従来通り、アプリの最初に起動するActivityのonCreateに[`Fox.trackInstall()`](../track_install/README.md)を実装します。

<div id="off_annotation_auto_session_tracking"></div>

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
> 従来通り、各ActivityのonResumeに`Fox.trackSession()`を実装します。

<div id="off_annotation_auto_deeplink_tracking"></div>

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
> 従来通り、MainとなるActivityのonResumeに`Fox.trackDeeplinkLaunch()`を実装します。

<div id="define_foxconfig_instance"></div>

#### 3.2.2 FoxConfigインスタンスを定義している場合

co.cyberz.fox.Fox.AUTOMATORの以下３つの設定を行います。いずれもデフォルトでfalseです。

* `setManualInstallTracking` : 初回起動計測の自動計測実行有無の設定
* `setManualSessionTracking` : セッション計測の自動計測実行有無の設定
* `setManualDeeplinkTracking` : ディープリンク系の計測の自動計測実行有無の設定

<div id="off_instance_auto_install_tracking"></div>

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
> 従来通り、アプリの最初に起動するActivityのonCreateに[`Fox.trackInstall()`](../track_install/README.md)を実装します。

<div id="off_instance_auto_session_tracking"></div>

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
> 従来通り、各ActivityのonResumeに`Fox.trackSession()`を実装します。

<div id="off_instance_auto_deeplink_tracking"></div>

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
> 従来通り、MainとなるActivityのonResumeに`Fox.trackDeeplinkLaunch()`を実装します。


---
[トップ](../../README.md)
