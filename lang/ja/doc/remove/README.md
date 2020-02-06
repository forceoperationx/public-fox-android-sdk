# F.O.X SDK の削除確認方法
<div id="delete_unity"></div>

# Android

## build.gradle

`build.gradle`に追加した設定を削除してください。

### F.O.X 4.X

```gradle
repositories {
    maven {
        url "https://github.com/forceoperationx/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    implementation 'co.cyberz.fox:track-core:{VERSION}'
}
```

```gradle
dependencies {
	implementation files('libs/foxtrack-core_{VERSION}.jar')
}
```

### F.O.X 3.X

```gradle
repositories {
    maven {
        url "https://github.com/cyber-z/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    compile 'co.jp.cyberz.fox:sdk-android:3.X.X'
}
```

以下で追加した設定で不要なものを削除してください。

[SDKの導入方法 4.X](https://github.com/forceoperationx/public-fox-android-sdk/blob/3.x/lang/ja/README.md#install_sdk)

[SDKの導入方法 3.X](https://github.com/forceoperationx/public-fox-android-sdk/blob/4.x/lang/ja/README.md#install_sdk)

## ライブラリの削除

libs配下に`FOX_Android_SDK_X.X.X.jar` または `AppAdForce.jar` がある場合は削除してください。


## コード/その他ファイル全体

F.O.Xでの計測のために追加したコードを削除してください。

また、コード/ファイル全体を対象に、以下の単語を検索し、含まれてる場合は適宜削除してください。
大文字小文字の区別なしで検索してください。

- `FOX`
- `F.O.X`
- `forceoperationx`
- `app-adforce`
- `appadforce`
- `cyberz`
- `cyber-z`
- `cyz`

