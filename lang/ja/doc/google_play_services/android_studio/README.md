[TOP](../../README.md)　>　[Google Play Servicesの導入](../README.md)　>　Android Studioプロジェクト

---

## Android StudioプロジェクトへのGoogle Play Servicesの導入

アプリケーションのモジュールディレクトリにあるbuild.gradleを開き、下記のように、最新のGoogle Play servicesへのdependenciesの設定を追記します。

```
dependencies {
	implementation 'com.google.android.gms:play-services:9.4.0'
}
```

## メソッド数の上限が64Kの仕様について

Androidアプリでは、保持できるメソッド数に64K(65536)の上限があり、超えるとビルドエラーが発生します。<br>
そのため多くのメソッドを有しているGooglePlayServicesを、広告IDの取得のためだけに導入されている場合は<br>
広告IDの取得を目的に切り出された以下のライブラリをお使いください。<br>
F.O.X SDKではGooglePlayServicesを広告IDの取得を目的に利用しています。

```
dependencies {
	implementation 'com.google.android.gms:play-services-ads:9.4.0'
}
```

> ※ メソッド数の上限が64Kの仕様についての詳細<br>
[Configure Apps with Over 64K Methods | Android Developers](https://developer.android.com/studio/build/multidex.html)

> ※ Google Play servicesの最新のバージョンはAndroidのデベロッパーサイトにて確認するようにしてください。<br>
[Google Play Services | Android Developers](https://developer.android.com/google/play-services/index.html)


---
[戻る](../README.md)

[トップ](../../../README.md)
