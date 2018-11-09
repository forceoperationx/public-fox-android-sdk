[TOP](../../README.md)　>　[Google Play Servicesの導入](../README.md)　>　Android Projects

---

## Setting Up Google Play Services with Android Projects

Open the build.gradle in the module directory of your application and add the latest Google Play services to dependency settings as below.

```
dependencies {
	compile 'com.google.android.gms:play-services:9.4.0'
}
```

## Maximum number of methods on Android (64 thousand)

Android applications have an upper limit of 64K (65536) for the number of methods that can be held, and build errors will occur if that number is exceeded. <br>
Therefore, because GooglePlayServices has many methods, if you are only implementing it to acquire the ad ID, <br> please use the library below which was created for acquiring said ad ID. <br>
In the F.O. X SDK, Google Play Services is used to acquire advertisement ID.

```
dependencies {
	compile 'com.google.android.gms:play-services-ads:9.4.0'
}
```

> ※ Regarding the maximum number of methods on Android<br>
[Configure Apps with Over 64K Methods | Android Developers](https://developer.android.com/studio/build/multidex.html)

> ※ Please check the latest version of Google Play services on the Android developer site.<br>
[Google Play Services | Android Developers](https://developer.android.com/google/play-services/index.html)


---
[Go back](../README.md)

[Return to Top](../../../README.md)
