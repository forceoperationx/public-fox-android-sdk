## Compliance to Google Play developer program

Force Operation X Android SDK is compliant to Google Play developer program policy. To be compliant to policy, in this SDK, advertisement ID is not acquired in the case of acquiring everlasting device ID（IMEI、MAC address and AndroidID）. From August, 1st, 2014, for all update uploaded into Google Play store and new application, it is necessary to advertisement ID for terminal ID using as advertising objectives. For being compliant to this policy, please follow the procedures below.

## Google Play Services SDK

To use the advertisement ID, Google Play Services SDK needs to be incorporated. In the case of not incorporating Google Play Services SDK into application, please follow the procedures in next site and conduct implementation.

[Setting Up Google Play Services | Android Developers](https://developer.android.com/google/play-services/setup.html)

## installation of Google Play Services SDK

Below, the method of implementing Google Play Services SDK is noted at December, 2014.

In the case of installing Google Play Services SDK, acquire the package from Android SDK Manager.

* Start Android SDK Manager.
* Put a check Google Play services under the Extras directory and install package.

![googlePlayServices01](./img01.png)

## The implementation of Google Play Services

[Set up Google Play Services for Eclipse project](./eclipse/README.md)

[Set up Google Play Services for Android Studio project](./android_studio/README.md)

## Setting for using Google Play Services

#### Setting of AndroidManifest.xml

To use Google Play Services, add the following setting in <Application>tag of AndroidManifest.xml.

```xml
<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />
```

#### Setting of Proguard

In the case of obfuscating with using Proguard, add the following setting.

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
[TOP](/lang/en/README.md)
