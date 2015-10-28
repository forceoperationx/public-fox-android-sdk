## 最新バージョンへのアップデートについて

以前のF.O.X SDKが導入されたアプリに最新のSDKを導入する際に必要な手順は以下の通りです。

1. 以前のバージョンの AppAdForce.jarがプロジェクトに組み込まれていれば、それらを削除
2.「2. SDK導入手順」に従い最新のAppAdForce.jarをプロジェクトに追加
3. Eclipse の「プロジェクト」→「クリーン」を実行


※「[（オプション）広告IDを利用するためのGoogle Play Services SDKの導入](../../google_play_services/ja/)」が未実施の場合には対応してください。


※「[（オプション）外部ストレージを利用した重複排除設定](../../external_storage/ja/)」が未実施の場合には対応してください。

SDK のアップデート後は、必ず効果測定テストを実施し、計測及びアプリケーションの動作に問題ないことを確認してください。


## 2.10.4g以前からのアップデートに関して

Android SDK v2.10.4g以前の海外版/グローバル版 SDK (バージョン末尾が u もしくは g)からSDKを最新版にアップデートをする場合、ID の下位互換性のために、次のように AdMangerインスタンスを生成するより以前の位置で必ずupdateFrom2_10_4g()をコールしてください。

本メソッドにより、v2.10.4g 以前にインストールされた端末において、LTV やアクセス解析の計測を継続することができるようになります。


```java
AdManager.updateFrom2_10_4g(); // 必ずnew AdManager より前でコールすること
AdManager ad = new AdManager(this);
ad.sendConversion("default");
```

> ※v2.10.4g以前のSDKを導入しリリースされたことがあるアプリケーション、例えば v2.10g→v2.10.5g→v2.10.6gとv2.10.4gより後のSDK からのアップデートであっても updateFrom2_10_4g の実装を外さないでください。


下記バージョンのSDKか過去に導入されたことがあるアプリケーションでは、本手順が必要となります。

* v2.7u / v2.7.1u / v2.7.2u
* v2.8u / v2.8.1u
* v2.9g / v2.9.1g / v2.9.2g
* v2.10g / v2.10.1g / v2.10.4g

> ※末尾に u や g がつかないバージョンからのアップデートの場合には本手順は必要ありません。


## v.2.14.6g以前からのアップデートに関して


「[インストールの計測の実装](/lang/ja/doc/send_conversion/README.md)」において、v2.14.7gよりURLスキーム経由からのアプリケーション起動時の成果計測に用いるメソッド名を下記のように変更しました。

<table>
  <tr>
    <td>変更後</td>
    <td>sendReengagementConversion(Intent)</td>
  </tr>
  <tr>
    <td>変更前</td>
    <td>setUrlScheme(Intenet)</td>
  </tr>
</table>

もし、setUrlSchemeメソッドを用いて実装している場合は、sendReengagementConversionメソッドを呼び出すように修正してください。

> ※現在のバージョンにおいてもsetUrlSchemeメソッドは利用可能ですが、将来のバージョンアップで削除される可能性があります。尚、sendReengagementConversion メソッドとsetUrlSchemeメソッドの内部処理は同一となっております。


---
[トップ](/lang/ja/README.md)
