## Google Play開發程序準則

Force Operation X Android SD遵循Google Play developer programme policy的準則。依據policy，若已經取得永久性的Device ID (IMEI、MAC address和Android ID)，將不會再取得廣告ID。2014年8月1日開始，上傳在Play store全部的更新和新APP中，對於為了廣告目的使用的設備ID必須使用廣告ID。為了符合此Policy，請按照下面的流程進行。

## Google Play Services SDK

為了能使用廣告ID，必須包含Google Play Services SDK。若APP當中沒有包含Google Play Services SDK，請依照以下網站的流程，進行導入。

[Setting Up Google Play Services | Android Developers](https://developer.android.com/google/play-services/setup.html)



## Google Play Services SDK的取得

下面記載了2014年11月公布的Google Play Services SDK導入方法。

如果沒有安裝Google Play Services SDK，請從Android SDK Manager取得Package。

* 起動Android SDK Manager
* 勾選Extras目錄下的Google Play services選項來安裝Package。

![googlePlayServices01](./img01.png)

## Google Play Services的導入

把Google Play Services導入到項目裡。
下面區分Eclipse和Android Studio來說明，請對照利用環境來從下面的鏈接來查看導入方法。


[在Eclipse項目裡導入Google Play Services](./eclipse/README.md)

[在Android Studio項目裡導入Google Play Services](./android_studio/README.md)



## 通過設定來利用Google Play Services

#### AndroidManifest.xml的編輯

為了利用Google Play Services，請在AndroidManifest.xml的&lt;application&gt;tag内追加下面的設定。

```xml
<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />
```

#### Proguard的設定

使用ProGuard進行APP讀取混淆化時，請追加下面的設定。

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
[TOP](/3.x/lang/zh-tw/README.md)
