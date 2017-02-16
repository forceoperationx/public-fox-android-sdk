## ディファードディープリンクの実装

ディファードディープリンクを実装することで、広告をクリックした際に紐付いているディープリンクにリダイレクトし<br>
アプリ内の対象のページに遷移することが可能となります。また、アプリが未インストールの場合でも、インストール後に<br>
広告のリダイレクト先となるディープリンクに遷移させることが可能となります。

> サポートバージョン：3.5.0以上


### AdManager
ディファードディープリンクの実装は、初回起動時のsendConversion実行時に以下の`registerDeeplinkCallback`メソッドと`sendDeeplinkConversion`メソッドを使用します。

|返り値|メソッド|実装箇所|説明|
|:---:|:---|:---:|:---|
|AdManager|registerDeeplinkCallback (int expire, <br>TimeUnit timeUnit, <br>[FoxDeeplinkListener](#foxdeeplinklistener) listener)<br><br>・`expire` : ラストクリックの有効期限<br>・`timeUnit` : 有効期限の単位（分/時間/日）<br>・[FoxDeeplinkListener](#foxdeeplinklistener) : ディープリンク受診時のコールバック|onCreate()|初回起動時に引数で指定した時間以内に発生したラストクリックをサーバーに問い合わせ、クリック情報が存在した場合、FoxDeeplinkListenerを介してディープリンクを返します。|
|AdManager|registerDeeplinkCallback ( )|onCreate()|初回起動時に引数で指定した時間以内に発生したラストクリックをサーバーに問い合わせ、クリック情報が存在した場合、ディープリンク先に自動遷移します。|
|void|sendDeeplinkConversion ( Intent i)<br>・`i` : Activity内で取得できるIntent|onResume()|アプリが初回起動直後に閉じられたり、ランディングページを開くなどでブラウザを起動したのちに、再度アプリがフォアグラウンドに戻った際、取得していたディープリンクのハンドリングを行うために必要となります。また、リエンゲージメント計測を行う場合にも実装が必須です。|

<div id="foxdeeplinklistener"></div>
### FoxDeeplinkListener
|返り値|メソッド|説明|
|:---:|:---|:---|
|void|onReceived (String deeplink) <br>・`deeplink` : 受信したディープリンク|クリック情報が存在し、ディープリンクを受信した際に呼ばれます。引数のdeeplinkには入稿時に設定したディープリンクを返します。（URLデコード等の編集も行いません）|
|void|onFailed ( ) |クリック情報が存在しない場合、或いはディープリンクを受信出来なかった(端末がオフライン時や通信に失敗した)場合に呼ばれます。|

### 実装例

以下の例に従って、いずれかを実装してください。

#### 例1 ディープリンクを取得してハンドリングする

```java
import jp.appAdForce.android.AdManager;

@Override
public void onCreate(Bundle savedInstanceState) {
	AdManager ad = new AdManager(this);
	ad.registerDeeplinkCallback(2, TimeUnit.DAYS, new FoxDeeplinkListener() {
      @Override
      public void onReceived(String deeplink) {
          // ディープリンクを取得した場合の処理
          Intent i = new Intent(Intent.ACTION_VIEW, Uri.parse(deeplink));
          i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
          startActivity(i);
      }

      @Override
      public void onFailed() {
          // 処理が失敗した場合の処理

      }
  ).sendConversion("default");
}

@Override
protected void onResume() {
    super.onResume();
    AdManager ad = new AdManager(this);
    ad.sendDeeplinkConversion(getIntent());
}
```

> * 上記は２日以内のラストクリックを有効とし、ディープリンクを取得する例となっています。
> * `onReceived`内ではディープリンク先に遷移する処理を実装します。

#### 例2 ディープリンクを取得し画面遷移をSDKに委譲する

```java
import jp.appAdForce.android.AdManager;

@Override
public void onCreate(Bundle savedInstanceState) {
	AdManager ad = new AdManager(this);
	ad.registerDeeplinkCallback()
	  .sendConversion("default");
}

@Override
protected void onResume() {
    super.onResume();
    AdManager ad = new AdManager(this);
    ad.sendDeeplinkConversion(getIntent());
}
```

> ※ ディープリンクを取得し、ディープリンク先への遷移をSDKが行う例となっています。遷移するタイミングはディープリンク取得直後となっており、コントロールすることはできません。<br>
また、ディープリンクを取得出来なかった場合や、遷移が不可能な無効なディープリンクの場合には反応しません。

> ※ ディープリンクに記号や特殊な文字を含む場合には、画面遷移の処理を本SDKに委譲せず`FoxDeeplinkListener`を用いて実装されることを推奨します。

<div id="with_thirdparty"></div>
### 他社ツールのディファードディープリンクを利用する

#### Facebook SDKの場合

以下の例では、F.O.X SDK のインストール計測完了のコールバック内で`FacebookSDK`のディファードディープリンク処理を記述しています。Cookie計測時のブラウザ立ち上げが完了した後にディファードディープリンクによる遷移が行われ、Cookie計測が正常に行われます。

```java
import jp.appAdForce.android.AdManager;
import jp.appAdForce.android.TrackingStateListener;

	@Override
	public void onCreate(Bundle savedInstanceState) {
			AdManager ad = new AdManager(this);
			ad.setTrackingStateListener(new TrackingStateListener() {
				@Override
				public void onComplete() {
					// Facebook SDK でのディファードディープリンク処理を実行

				}
			}).sendConversion("default");
	}

	@Override
	protected void onResume() {
			super.onResume();
			AdManager ad = new AdManager(this);
			ad.sendDeeplinkConversion(getIntent());
	}
```

> Facebook SDKの実装に関しては[公式ドキュメント](https://developers.facebook.com/docs/app-ads/deep-linking#deferred-deep-linking)を参照ください。

---
[TOP](/lang/en/README.md)
