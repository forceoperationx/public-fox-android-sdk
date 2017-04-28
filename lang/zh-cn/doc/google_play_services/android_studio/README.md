## 在Android Studio項目裡導入Google Play Services

打開APP的module directory裡的build.gradle，像下面那樣追加對最新Google Play services的dependencies設定。

```
dependencies {
	compile 'com.google.android.gms:play-services:6.1.+'
}
```

> Google Play services的最新版本請在Android的開發網站進行確認

[Google Play Services | Android Developers](https://developer.android.com/google/play-services/index.html)


![googlePlayServices01](./img01.png)

---
[返回](../README.md)
