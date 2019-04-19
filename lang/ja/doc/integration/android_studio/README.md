[TOP](../../../README.md)　>　Android Studioプロジェクトへの導入

---

# Android Studioプロジェクトへの導入

## Gradleを使用して導入する場合

build.gradleに下記の設定を適切な場所に追加してください。

```
repositories {
    maven {
        url "https://github.com/forceoperationx/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    implementation 'co.cyberz.fox:track-core:{VERSION}'
}
```

## 手動で導入する場合

Android StudioプロジェクトへAppAdForce.jarを追加する方法は以下の通りです。

* アプリケーションプロジェクトの「libs」フォルダー配下にAppAdForce.jarを配置します。


![integration01](./img01.png)

アプリケーションのモジュールディレクトリにあるbuild.gradleを開き、foxtrack-core_{VERSION}.jarへのパスを追記します。

```
dependencies {
	implementation files('libs/foxtrack-core_{VERSION}.jar')
}
```

![integration02](./img02.png)


---
[トップ](../../../README.md)
