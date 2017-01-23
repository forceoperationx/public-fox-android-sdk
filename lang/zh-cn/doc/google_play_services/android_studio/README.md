[TOP](../../README.md)　>　[导入Google Play Services](../README.md)　>　Android Studio项目

---

## 在Android Studio项目中导入Google Play Services

打开APP中Moudle目录里的build.gradle，如下所示，在最新Google Play services中添加依存关系（dependencies）设置。

```
dependencies {
	compile 'com.google.android.gms:play-services:9.4.0'
}
```

## 关于方法数的上限为64k的式样

Android APP持有的方法数上限为64K(65536)，超过后会发生编译错误。
因此仅为获取广告ID而导入拥有大量方法的GooglePlayServices时，
请使用下列摒弃广告ID获取目的的类库。
在F.O.X SDK中用GooglePlayServices来实现获取广告ID的目的

```
dependencies {
	compile 'com.google.android.gms:play-services-ads:9.4.0'
}
```

> ※ 方法数上限为64k的式样详情<br>
[Configure Apps with Over 64K Methods | Android Developers](https://developer.android.com/studio/build/multidex.html)

> ※ Google Play services的最新版本请在Android开发网站中确认。<br>
[Google Play Services | Android Developers](https://developer.android.com/google/play-services/index.html)

---
[返回](../README.md)

[Top](../../../README.md)
