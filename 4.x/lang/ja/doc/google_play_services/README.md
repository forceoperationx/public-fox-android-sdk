[TOP](/4.x/lang/ja/README.md)　>　Google Play Servicesの導入

---

# Google Play Servicesの導入

## Google Playデベロッパープログラムへの準拠

Force Operation X Android SDKはGoogle Playデベロッパープログラムポリシーに準拠しています。本SDKはポリシーに準拠するために、永続的なデバイス ID（IMEI、MACアドレス及びAndroidID）が取得される場合には広告IDが取得されません。2014年8月1日から、Google Playストアにアップロードされたすべての更新や新着アプリには、広告目的で使用する端末IDには広告ID を利用する必要があります。本ポリシーに準拠するために、以下の手順を行ってください。


## Google Play Services SDK


広告IDを利用できるようにするために、Google Play Services SDKが組み込まれている必要があります。
アプリケーションにGoogle Play Services SDKが組み込まれていない場合には、次のサイトの手順に従い、導入を行ってください。

[Setting Up Google Play Services | Android Developers](https://developer.android.com/google/play-services/setup.html)



## Google Play Services SDKの取得

以下に、2014年11月時点での、Google Play Services SDKの導入方法を記載致します。


Google Play Services SDKをインストールしていない場合は、Android SDK Managerからパッケージを取得します。

* Android SDK Managerを起動します。
* Extrasディレクトリ配下のGoogle Play servicesにチェックをいれ、パッケージをインストールします。

![googlePlayServices01](./img01.png)

## Google Play Servicesの導入

Google Play Servicesをプロジェクトに取り込みます。
EclipseとAndroid Studioに分けて説明しますので、ご利用の環境に合わせて次のリンクから導入方法を参照ください。


[EclipseプロジェクトへのGoogle Play Servicesの導入](./eclipse/README.md)

[Android StudioプロジェクトへのGoogle Play Servicesの導入](./android_studio/README.md)



## Google Play Servicesを利用するための設定

#### AndroidManifest.xmlの編集

Google Play Servicesを利用するために下記の設定をAndroidManifest.xmlの<application>タグ内に記述します。

```xml
<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />
```

> ※ Android Studioで導入する場合、上記のバージョン指定は不要です。

#### Proguardの設定

Proguardを利用して難読化している場合は、以下の設定を追加してください。

```
-keep class * extends java.util.ListResourceBundle {
    protected Object[][] getContents();
}

-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable {
    public static final *** NULL;
}

-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * {
    @com.google.android.gms.common.annotation.KeepName *;
}

-keepnames class * implements android.os.Parcelable {
    public static final ** CREATOR;
}
```

---
[トップ](../../README.md)
