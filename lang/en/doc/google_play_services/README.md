[TOP](../../README.md)　>　Setting Up Google Play Services

---

# Setting Up Google Play Services

## Compliance with the Google Play Developer Program

 The Force Operation X Android SDK complies with the Google Play Developer Program Policy. In accordance with said policy, this SDK does not acquire the advertisement ID if a permanent device ID (IMEI, MAC address and Android ID) is acquired. From August 1, 2014, for all updates and new apps uploaded to the Google Play store, you need to use the advertisement ID if the device ID is to be used for advertising purposes. To comply with this policy, please follow the procedure below.


## Google Play Services SDK

The Google Play Services SDK must be set up in order to obtain an ad ID.
If your application does not include the Google Play Services SDK, follow the instructions in the following link to do so.

[Setting Up Google Play Services | Android Developers](https://developer.android.com/google/play-services/setup.html)



## Installing the Google Play Services SDK

If you do not have the Google Play Services SDK installed, get the package from the Android SDK Manager.

* Start the Android SDK Manager
* Find Google Play services under the Extras directory and install the package

![googlePlayServices01](./img01.png)

## Setting Up Google Play Services

Set up Google Play Services inside your project.
Eclipse and Android Studio will be explained separately, so refer to the installation method from the following links according to your environment.

[Eclipse projects](./eclipse/README.md)

[Eclipse projects](./android_studio/README.md)



## Setting Up Google Play Services for use

#### Editing your AndroidManifest.xml

In order to use Google Play Services, add the following settings in the `application` tag of `AndroidManifest.xml`.

```xml
<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />
```

> ※ When installing with Android Studio, the above version specification is unnecessary.

#### Proguard Settings

If you are using Proguard, add the following settings.

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
[Return to Top](../../README.md)
